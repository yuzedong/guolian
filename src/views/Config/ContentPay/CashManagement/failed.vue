<template>
    <section class="failed_main">

        <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
             @handleSearch="handleSearch"
             @handleFocus='handleFocus'
		></search-header>
        <div class="balance_form">
            <base-table v-bind="formList"
             @handleDetail="handleTableIssue"
              @sortChange="handleSortChange"
              @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange">
                <template #withdrawType="scope">
                    <p>{{scope.scope.row.withdrawType==1?'支付宝':scope.scope.row.withdrawType==2?'微信':''}}</p>
                </template>
                <template #accountFullNameStr="scope">
                    <div class="main_name">
                        <img v-if="scope.scope.row.headimgurl" :src="scope.scope.row.headimgurl" alt="" style="width:34px;height:34px;margin-right:10px;border-radius:50%;">
                        <el-tooltip :content="scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName" placement="bottom" effect="light">
                            <p class="sonp z-zhanghao-hide">{{scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName}}</p>
                        </el-tooltip>

                    </div>
                </template>
                <template #remark="scope">
                    <el-tooltip :content="scope.scope.row.remark" placement="bottom" effect="light">
                        <p class="remarkp z-width-md" >{{scope.scope.row.remark}}</p>
                    </el-tooltip>
                </template>
            </base-table>
        </div>
    </section>
</template>
<script>
import searchHeader from '@/components/mixins/searchHeader'
import baseTable from '@/components/mixins/baseTable'
import { getTime, getData, getNewTime } from '@/utils'

export default {
  name: 'failed',
  mixins: [searchHeader, baseTable],
  props: {
    activeTab: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      searchHeader: {
        searchItems: [
          {
            type: 'cutButton',
            value: 'time2',
            label: '时间:',
            tabClass: 'qiehuan',
            options: [
              {
                label: '今日 ',
                value: 1
              },
              {
                label: '近7天',
                value: 2
              },
              {
                label: '近30天',
                value: 3
              },
              {
                label: '累计',
                value: 4
              }
            ]
          },
          {
            label: '日期区间:',
            type: 'dataPicker',
            rangeText: '-',
            value: 'time',
            pickerOptions: {
              disabledDate (time) {
                return time.getTime() > Date.now() - 8.64e6
              }
            },
            style: {
              width: '318px'
            }
          },
          {
            type: 'select',
            class: 'z-hidden-lg-and-down',
            label: '提现方式',
            value: 'withdrawType',
            style: {
              width: '120px'
            },
            options: [
              {
                label: '全部',
                value: ''
              },
              {
                label: '微信支付',
                value: 2
              },
              {
                label: '支付宝支付',
                value: 1
              }
            ]
          },
          {
            type: 'searchInput',
            value: 'nameOrAccountOrTransactionSerial',
            class: 'z-hidden-lg-and-down',
            placeholder: '请输入用户名/提现账号',
            style: {
              width: '335px'
            }
          },
          {
            btnType: 'Search',
            type: 'henderBorderBtn',
            text: '导出明细',
            icon: 'xiangguanneirong',
            name: 'export'
          },
          {
            type: 'senior',
            value: 'senior',
            class: 'z-hide-md-and-up z-hidden-lg-and-up'
          }
        ]
      },
      formList: {
        columns: [
          {
            label: '申请人用户名',
            prop: 'userName'
            // width:'140'
          },
          {
            label: '提现金额(元)',
            prop: 'amount',
            align: 'left',
            sortable: 'custom'
            // width:'150'
          },
          {
            label: '转账时间',
            prop: 'applicationTime',
            align: 'left',
            sortable: 'custom'
          },
          {
            label: '提现方式',
            prop: 'withdrawType',
            align: 'left',
            scopeType: 'slot'
          },
          {
            label: '提现账号',
            prop: 'accountFullNameStr',
            align: 'left',
            scopeType: 'slot',
            width: '360'
          },
          {
            label: '失败原因',
            prop: 'remark',
            align: 'left',
            scopeType: 'slot',
            width: '260'
          }
        ],
        handleColumnProp: {
          label: '操作',
          width: '120'
        },
        handleColumn: [
          {
            type: 'Detail',
            name: '再次转账',
            icon: 'zhaunzhang',
            disabled: false,
            iconStyle: {
              fontSize: '14px',
              fill: '#1EC6DF'
            }
          }

        ],
        showSelection: false,
        showPagination: true,
        showIndex: false,
        data: [],
        totalCount: 0
      },
      list: {
        params: {
          startDate: '',
          endDate: '',
          withdrawType: '',
          orderType: 12,
          userId: '',
          userName: '',
          nameOrAccountOrTransactionSerial: '',
          time: [],
          time2: 4,
          page: 1,
          size: 10
        },
        filterParams: ['time', 'time2']
      }

    }
  },
  methods: {
    getTime,
    getData,
    getNewTime,
    // 排序
    handleSortChange (column, prop, order) {
      let asc = 'ascending'
      let des = 'descending'
      if (prop == 'amount' && order == asc) {
        this.list.params.orderType = 1
      } else if (prop == 'amount' && order == des) {
        this.list.params.orderType = 2
      } else if (prop == 'applicationTime' && order == asc) {
        this.list.params.orderType = 11
      } else if (prop == 'applicationTime' && order == des) {
        this.list.params.orderType = 12
      } else {
        this.list.params.orderType = 12
      }
      this.fetchpaycontentCashmanagementfailed()
    },
    // 搜索
    handleEventBtnSearch (item) {
      this.$request.fetchpaycontentCashmanagementfailedExport(this.list.params).then(res => {
        this.$exportClick(res, '提现失败.xlsl')
      })
    },
    handleFocus () {
      this.list.params.time2 = ''
      if (this.list.params.time === null) {
        this.list.params.time2 = 4
        this.list.params.time = []
        this.list.params.startDate = ''
        this.list.params.endDate = ''
      }
    },
    handleSearch () {
      this.list.params.userName = this.list.params.title
      if (this.list.params.time2 !== '') {
        this.list.params.time = []
      }

      if (this.list.params.time2) {
        if (this.list.params.time2 === 1) {
          this.list.params.startDate = this.getData(0)
          this.list.params.endDate = this.getData(0)
        } else if (this.list.params.time2 === 2) {
          this.list.params.startDate = this.getData(6)
          this.list.params.endDate = this.getData(0)
        } else if (this.list.params.time2 === 3) {
          this.list.params.startDate = this.getData(29)
          this.list.params.endDate = this.getData(0)
        } else if (this.list.params.time2 === 4) {
          this.list.params.startDate = ''
          this.list.params.endDate = ''
        }
        this.list.params.time[0] = this.list.params.startDate
        this.list.params.time[1] = this.list.params.endDate
      } else if (this.list.params.time === null) {
        this.list.params.time2 = 4
        this.list.params.time = []
        this.list.params.startDate = ''
        this.list.params.endDate = ''
      }
      if (this.list.params.time.length === 2) {
        this.list.params.startDate = this.list.params.time[0]
        this.list.params.endDate = this.list.params.time[1]
      }
      this.list.params.page = 1
      // if (this.list.params.time) {
      //     this.list.params.startDate = this.list.params.time[0]
      //     this.list.params.endDate = this.list.params.time[1]
      // }
      this.fetchpaycontentCashmanagementfailed()
    },
    handleTableIssue (val) {
      this.$request.fetchpaycontentCashmanagementfailedAgain(val.id).then(res => {
        if (res.code == 200) {
          this.$message.success(res.message)
          this.fetchpaycontentCashmanagementfailed()
        } else {
          this.$message(res.message)
          this.fetchpaycontentCashmanagementfailed()
        }
      })
    },
    fetchpaycontentCashmanagementfailed () {
      this.$request.fetchpaycontentCashmanagementfailed(this.list.params).then(res => {
        if (res.code == 200) {
          this.formList.data = res.data.content
          this.formList.totalCount = res.data.totalElements
        }
      })
    },
    handleSizeChange (val) {
      this.list.params.size = val
      this.fetchpaycontentCashmanagementfailed()
    },
    handleCurrentChange (val) {
      this.list.params.page = val
      this.fetchpaycontentCashmanagementfailed()
    }
  },
  mounted () {
    this.fetchpaycontentCashmanagementfailed()
  },
  watch: {
    activeTab (num) {
      if (num == 3) {
        this.fetchpaycontentCashmanagementfailed()
      }
    }
  }
}
</script>
<style lang="scss">
    .failed_main{
        .qiehuan{
            .el-radio-button__inner{
                 min-width: 74px;
            }
        }
        @media screen and (max-width: 1919px) {
            .z-width-md {
                width: 180px !important;
            }
            .z-zhanghao-hide {
                width: 200px;
                white-space: pre-wrap;
            }
        }
        .remarkp{
            white-space:nowrap;
            overflow:hidden;
            text-overflow: ellipsis;
            width: 200px;
            cursor: pointer;
        }
        .personclass{
            .el-link--default{
                color: #1EC6DF !important;
            }
        }
        .main_name{
            display: flex;
            .sonp{
                white-space:nowrap;
                overflow:hidden;
                text-overflow: ellipsis;
                width: 300px;
                cursor: pointer;
                line-height: 34px;
            }
        }
        .top{
            display: flex;
            margin-bottom: 20px;
            p{
                margin-right: 50px;
            }
        }
    }
</style>

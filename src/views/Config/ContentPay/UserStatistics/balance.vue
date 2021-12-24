<template>
    <section class="balance_main">
        <div class="top">
            <p class="topname">用户总余额(元)：<span>{{Number(balanceStatistic.balance)}}</span></p>
            <p class="shxiang">|</p>
            <p>累计总提现(元)：<span>{{Number(balanceStatistic.withdrawalAmount)}}</span></p>
            <p class="shxiang">|</p>
            <p>用户累计总收益(元)：<span>{{Number(balanceStatistic.userTotalAmount)}}</span></p>
            <p v-if="platformOpen" class="shxiang">|</p>
            <p v-if="platformOpen">平台累计收益(元)：<span>{{Number(balanceStatistic.platformTotalAmount)}}</span></p>
        </div>
        <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
            @handleSearch="handleSearch"
		></search-header>

        <div class="balance_form">
            <base-table v-bind="formList"
             @sortChange="handleSortChange"
             @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange">
                <template #username="scope">
                    <p :class="scope.scope.row.deleteFlag?'delcont':''">{{scope.scope.row.username}} <span v-if="scope.scope.row.deleteFlag">已删除</span></p>
                </template>
            </base-table>
        </div>
    </section>
</template>
<script>
import searchHeader from '@/components/mixins/searchHeader'
import baseTable from '@/components/mixins/baseTable'
import qs from 'qs'

export default {
  name: 'balance',
  mixins: [searchHeader, baseTable],
  props: {
    activeTab: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      platformOpen: false,
      balanceStatistic: {
        balance: 0,
        withdrawalAmount: 0,
        userTotalAmount: 0,
        platformTotalAmount: 0
      },
      searchHeader: {
        searchItems: [
          {
            type: 'searchInput',
            value: 'userName',
            placeholder: '请输入用户名',
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
          }
        ]
      },
      formList: {
        columns: [
          {
            label: '用户名',
            prop: 'username',
            scopeType: 'slot'
          },
          {
            label: '账户余额(元)',
            prop: 'balance',
            align: 'left',
            sortable: 'custom'
          },
          {
            label: '已提现(元)',
            prop: 'withdrawalAmount',
            align: 'left',
            sortable: 'custom'
          },
          {
            label: '累计收益(元)',
            prop: 'totalAmount',
            align: 'left',
            sortable: 'custom'
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
          userName: '',
          orderType: 2,
          page: 1,
          size: 10
        }
      }
    }
  },
  methods: {
    // 判断平台收益开关
    handlePlatformOpen () {
      this.$request.fetchPayConfig().then(res => {
        if (res.code == 200) {
          // console.log(res.data);
          this.platformOpen = res.data.platform
          // if (res.data.platform==0) {
          //   this.platformOpen = false
          // }else if (res.data.platform==1) {
          //     this.platformOpen = true
          // }
        }
      })
    },
    // 排序
    handleSortChange (column, prop, order) {
      // console.log(column,prop,order);
      let asc = 'ascending'
      let des = 'descending'
      if (prop == 'balance' && order == asc) {
        this.list.params.orderType = 1
      } else if (prop == 'balance' && order == des) {
        this.list.params.orderType = 2
      } else if (prop == 'withdrawalAmount' && order == asc) {
        this.list.params.orderType = 3
      } else if (prop == 'withdrawalAmount' && order == des) {
        this.list.params.orderType = 4
      } else if (prop == 'totalAmount' && order == asc) {
        this.list.params.orderType = 5
      } else if (prop == 'totalAmount' && order == des) {
        this.list.params.orderType = 6
      } else {
        this.list.params.orderType = 2
      }
      this.fetchUserStatisticBalance()
    },
    // 导出
    handleEventBtnSearch (item) {
      // console.log(item);
      let token = localStorage.getItem('JEECMS-Auth-Token')
      window.open(`${this.$path}${this.$prefix}/payuserstatistic/balance/export?${qs.stringify(this.list.params)}&JEECMS-Auth-Token=${token}`)
    },
    handleSearch () {
      // console.log(this.list.params);
      this.fetchUserStatisticBalance()
    },
    // 表格数据
    fetchUserStatisticBalance () {
      delete this.list.params.time
      delete this.list.params.time2
      this.$request.fetchUserStatisticBalance(this.list.params).then(res => {
        if (res.code == 200) {
          this.formList.data = res.data.content
          this.floatData()
          this.formList.totalCount = res.data.totalElements
        }
      })
    },
    floatData () {
      this.formList.data.forEach((item, index) => {
        this.formList.data[index].balance = Number(item.balance)
        this.formList.data[index].platformTotalAmount = Number(item.platformTotalAmount)
        this.formList.data[index].userTotalAmount = Number(item.userTotalAmount)
        this.formList.data[index].withdrawalAmount = Number(item.withdrawalAmount)
        this.formList.data[index].rewardAmount = Number(item.rewardAmount)
        this.formList.data[index].totalAmount = Number(item.totalAmount)
      })
    },
    fetchBalanceStatistic () {
      this.$request.fetchBalanceStatistic(this.list.params).then(res => {
        if (res.code == 200) {
          // console.log(res);
          this.balanceStatistic = res.data
        }
      })
    },
    handleSizeChange (val) {
      this.list.params.size = val
      this.fetchUserStatisticBalance()
    },
    handleCurrentChange (val) {
      this.list.params.page = val
      this.fetchUserStatisticBalance()
    }
  },
  mounted () {
    this.fetchUserStatisticBalance()
    this.fetchBalanceStatistic()
    this.handlePlatformOpen()
  },
  watch: {
    activeTab (num) {
      if (num == 1) {
        this.fetchUserStatisticBalance()
        this.fetchBalanceStatistic()
      }
    }
  }
}
</script>
<style lang="scss" >
    .balance_main{
        .delcont{
            color: #ccc;
            span{
                color: #ccc;
            }
        }
        .top{
            display: flex;
            margin-top: 6px;
            margin-bottom: 30px;
            .shxiang{
                color: #999;
                line-height: 23px;
                font-size: 15px;
            }
            p{
                font-family: Microsoft YaHei;
                font-weight: 400;
                color: #666666;
                margin-right: 15px;
                font-size: 14px;
                span{
                    font-family: Microsoft YaHei;
                    font-weight: bold;
                    color: #333333;
                    font-size: 20px;
                }
            }
        }
    }
</style>

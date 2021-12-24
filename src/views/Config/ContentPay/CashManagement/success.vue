<template>
    <section class="success_main">

        <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
            @handleSearch="handleSearch"
             @handleFocus='handleFocus'
		></search-header>
        <div class="top">
            <p>提现总金额(元)：<span>{{Number(successdata.withdrawalAmount)}}</span></p>
            <p class="shuxian">|</p>
            <p>微信(元)：<span>{{Number(successdata.withdrawalAmountWechat)}}</span></p>
            <p class="shuxian">|</p>
            <p>支付宝(元)：<span>{{Number(successdata.withdrawalAmountAlipay)}}</span></p>
        </div>
        <div class="balance_form">
            <base-table v-bind="formList"
             @handleIssue="handleTableIssue"
             @sortChange="handleSortChange"
             @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange">
                 <template #withdrawType="scope">
                    <p>{{scope.scope.row.withdrawType==1?'支付宝':scope.scope.row.withdrawType==2?'微信':''}}</p>
                </template>
                <template #accountFullNameStr="scope">
                     <div class="main_name">
                        <img v-if="scope.scope.row.headimgurl" :src="scope.scope.row.headimgurl" alt="" style="width:34px;height:34px;margin-right:10px;border-radius:50%;">
                        <!-- <p class="sonp z-width-md" :title="scope.scope.row.accountFullNameStr">{{scope.scope.row.accountFullNameStr}}</p> -->
                        <el-tooltip :content="scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName" placement="bottom" effect="light">
                            <p class="sonp z-width-md">{{scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName}}</p>
                        </el-tooltip>

                    </div>
                </template>
            </base-table>
        </div>
    </section>
</template>
<script>
import searchHeader from '@/components/mixins/searchHeader'
import baseTable from '@/components/mixins/baseTable'
import {  getTime, getData, getNewTime } from '@/utils'

export default {
    name:'success',
    mixins: [searchHeader,baseTable],
    props:{
        activeTab:{
            type:String,
            default:''
        }
    },
    data(){
        return{
            successdata:{
                withdrawalAmount:0,
                withdrawalAmountAlipay:0,
                withdrawalAmountWechat:0
            },
            searchHeader: {
                searchItems: [
                        {
                        type: 'cutButton',
                        value: 'time2',
                        label: '时间:',
                        tabClass:'qiehuan',
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
                        label:'日期区间:',
                        type: 'dataPicker',
                        rangeText: '-',
                        value: 'time',
                        pickerOptions: {
                            disabledDate (time) {
                                return time.getTime() > Date.now() - 8.64e6
                            }
                        },
                        style:{
                            width:'318px'
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
                        options:[
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
                        class: 'z-hidden-lg-and-down',
                        value: 'nameOrAccountOrTransactionSerial',
                        placeholder: '请输入用户名/流水号/提现账号',
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
                        class:'z-hide-md-and-up z-hidden-lg-and-up'
                    }
                ]
            },
            formList: {
                 columns: [
                    {
                        label: '申请人用户名',
                        prop: 'userName',
                        // width:'180'
                    },
                    {
                        label: '提现金额(元)',
                        prop: 'amount',
                         align: 'left',
                        sortable: 'custom',
                        minWidth:'100'
                    },
                    {
                        label: '提现时间',
                        prop: 'applicationTime',
                        align: 'left',
                        sortable: 'custom',
                        // width:'140'
                    },
                     {
                        label: '提现方式',
                        prop: 'withdrawType',
                        align: 'left',
                        scopeType:'slot',
                        // width:''
                    },
                    {
                        label: '提现账号',
                        prop: 'accountFullNameStr',
                        align: 'left',
                        width:'300',
                        scopeType:'slot'
                    },
                     {
                        label: '流水号',
                        prop: 'transactionSerialNum',
                        align: 'left',
                        width:'300'
                    }
                ],

                showSelection: false,
                showPagination: true,
                showIndex: false,
                data: []
            },
            list:{
                params:{
                    startDate:'',
                    endDate:'',
                    withdrawType:'',
                    orderType:6,
                    userId:'',
                    userName:'',
                    nameOrAccountOrTransactionSerial:'',
                    time:[],
                    time2:4,
                    page:1,
                    size:10
                },
                filterParams:['time','time2']
            }

        }
    },
    methods:{
         getTime,
        getData,
        getNewTime,
          // 排序
        handleSortChange(column, prop, order){
            // console.log(prop);
            let asc = 'ascending'
            let des = 'descending'
            if (prop=='amount'&&order==asc) {
                this.list.params.orderType = 1
            }else if (prop=='amount'&&order==des) {
                this.list.params.orderType = 2
            }else if (prop=='applicationTime'&&order==asc) {
                this.list.params.orderType = 11
            }else if (prop=='applicationTime'&&order==des) {
                this.list.params.orderType = 12
            }else{
                this.list.params.orderType = 6
            }
            this.fetchpaycontentCashmanagementSuccess()
        },
        // 搜索
		handleEventBtnSearch(item) {
			this.$request.fetchpaycontentCashmanagementSuccessExport(this.list.params).then(res=>{
                this.$exportClick(res, '提现成功.xlsl')
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
         handleSearch(){
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

            // if (this.list.params.time) {
            //     this.list.params.startDate = this.list.params.time[0]
            //     this.list.params.endDate = this.list.params.time[1]
            // }
            this.fetchpaycontentCashmanagementSuccess()
            this.fetchpaycontentCashmanagementSuccessStatistic()
        },
        handleTableIssue(){
            this.$routerLink('/config/content-pay/user-statistics/detail')
        },
        fetchpaycontentCashmanagementSuccess(){
            // console.log(this.list.params);
            this.$request.fetchpaycontentCashmanagementSuccess(this.list.params).then(res=>{
                if (res.code==200) {
                    // console.log(res);
                    this.formList.data = res.data.content
                    this.formList.totalCount = res.data.totalElements
                }
            })
        },
        // 统计数据
        fetchpaycontentCashmanagementSuccessStatistic(){
            this.$request.fetchpaycontentCashmanagementSuccessStatistic(this.list.params).then(res=>{
                if (res.code==200) {
                    this.successdata = res.data
                    // console.log(res);
                }
            })
        },
         handleSizeChange (val) {
            this.list.params.size = val
            this.fetchpaycontentCashmanagementSuccess()
        },
        handleCurrentChange (val) {
            this.list.params.page = val
            this.fetchpaycontentCashmanagementSuccess()
        }
    },
    mounted(){
        this.fetchpaycontentCashmanagementSuccess()
        this.fetchpaycontentCashmanagementSuccessStatistic()
    },
    watch:{
        activeTab(num){
            if (num==2) {
                this.fetchpaycontentCashmanagementSuccess()
                this.fetchpaycontentCashmanagementSuccessStatistic()
            }
        }
    }
}
</script>
<style lang="scss">
    .success_main{
        .qiehuan{
            .el-radio-button__inner{
                 min-width: 74px;
            }
        }
        @media screen and (max-width: 1919px) {
            .z-width-md {
                white-space: pre-wrap !important;
                overflow: auto !important;
            }
        }
        .main_name{
            display: flex;
            .sonp{
                white-space:nowrap;
                overflow:hidden;
                text-overflow: ellipsis;
                width: 250px;
                cursor: pointer;
                line-height: 34px;
            }
        }
        .top{
            display: flex;
            margin-bottom:30px;
            margin-top: 12px;
            p{
                font-size: 14px;
                color: #666;
                span{
                    font-size: 20px;
                    font-weight: bold;
                    color: #333;
                }
            }
            .shuxian{
                font-size: 15px;
                color: #999;
                margin: 0px 15px;
                line-height: 20px;
                line-height: 23px;
            }
        }
    }
</style>

<template>
    <section class="withdrawdetail_main">
        <h3 style="margin-bottom:30px;">用户名：{{username}}</h3>

         <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="params"
			@handleBtnSearch="handleEventBtnSearch"
             @handleSearch="handleSearch"
            @handleFocus='handleFocus'
		></search-header>
         <div class="top">
            <p>总提现金额(元)：<span>{{withdrawData.withdrawalAmount}}</span></p>
            <p class="shuxian">|</p>
            <p>微信提现(元)：<span>{{withdrawData.withdrawalAmountWechat}}</span></p>
            <p class="shuxian">|</p>
            <p>支付宝提现(元)：<span>{{withdrawData.withdrawalAmountAlipay}}</span></p>
        </div>
        <div class="balance_form">
            <base-table v-bind="formList"
                @sortChange="handleSortChange"
                @handleSizeChange="handleSizeChange"
                @handleCurrentChange="handleCurrentChange">
            <template #withdrawType="scope">
                <p>{{scope.scope.row.withdrawType==1?'支付宝':scope.scope.row.withdrawType==2?'微信':''}}</p>
            </template>
            <template #accountFullNameStr="scope">
                    <div class="main_name">
                        <img v-if="scope.scope.row.headimgurl" :src="scope.scope.row.headimgurl" alt="" style="width:34px;height:34px;margin-right:10px;border-radius:50%;">
                        <!-- <el-tooltip :content="scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName" placement="bottom" effect="light"> -->
                            <p class="sonp">{{scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName}}</p>
                        <!-- </el-tooltip> -->
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
    name:'configUserStatisticsWithdrawdetail',
    mixins: [searchHeader,baseTable],
    data(){
        return{
            username:'',
            withdrawData:{
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
                        label: '提现方式',
                        value: 'withdrawType',
                        class: 'z-hidden-lg-and-down',
                        style: {
                        width: '150px'
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
                            },
                        ]
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
                        label: '金额(元)',
                        prop: 'amount',
                        sortable: 'custom',
                        width:'160'
                    },
                    {
                        label: '提现方式',
                        prop: 'withdrawType',
                        scopeType:'slot',
                        width:'160'
                    },
                     {
                        label: '提现账号',
                        prop: 'accountFullNameStr',
                        scopeType:'slot'
                    },
                     {
                        label: '提现申请时间',
                        prop: 'createTime',
                        sortable: 'custom'

                    },
                     {
                        label: '审核通过时间',
                        prop: 'reviewTime',
                        sortable: 'custom'

                    },
                     {
                        label: '流水号',
                        prop: 'transactionSerialNum',
                        width:'300'
                    }
                ],

                showSelection: false,
                showPagination: true,
                showIndex: false,
                data: [],
                totalCount:0
            },
            params:{
                startDate:'',
                endDate:'',
                withdrawType:'',
                orderType:'',
                userId:'',
                time:[],
                time2:4,
                page:1,
                size:10
            },
            filterParams:['time','time2']
        }
    },
    methods:{
         getTime,
        getData,
        getNewTime,
          // 排序
        handleSortChange(column, prop, order){
            let asc = 'ascending'
            let des = 'descending'
            if (prop=='amount'&&order==asc) {
                this.params.orderType = 1
            }else if (prop=='amount'&&order==des) {
                this.params.orderType = 2
            }else if (prop=='createTime'&&order==asc) {
                this.params.orderType = 3
            }else if (prop=='createTime'&&order==des) {
                this.params.orderType = 4
            }else if (prop=='reviewTime'&&order==asc) {
                this.params.orderType = 5
            }else if (prop=='reviewTime'&&order==des) {
                this.params.orderType = 6
            }else{
                this.params.orderType = 7
            }
            this.fetchpaycontentWithdrawDetail()
        },
        // 搜索
		handleEventBtnSearch(item) {
			this.$request.fetchpaycontentWithdrawDetailExport(this.params).then(res=>{
               this.$exportClick(res, '提现明细.xlsl')
            })
		},
         handleFocus () {
            this.params.time2 = ''
            if (this.params.time === null) {
                this.params.time2 = 4
                this.params.time = []
                this.params.startDate = ''
                this.params.endDate = ''
            }
        },
         handleSearch(){
            this.params.userName = this.params.title
            if (this.params.time2 !== '') {
                this.params.time = []
            }

            if (this.params.time2) {
                if (this.params.time2 === 1) {
                this.params.startDate = this.getData(0)
                this.params.endDate = this.getData(0)
                } else if (this.params.time2 === 2) {
                this.params.startDate = this.getData(6)
                this.params.endDate = this.getData(0)
                } else if (this.params.time2 === 3) {
                this.params.startDate = this.getData(29)
                this.params.endDate = this.getData(0)
                } else if (this.params.time2 === 4) {
                this.params.startDate = ''
                this.params.endDate = ''
                }
                this.params.time[0] = this.params.startDate
                this.params.time[1] = this.params.endDate
            } else if (this.params.time === null) {
                this.params.time2 = 4
                this.params.time = []
                this.params.startDate = ''
                this.params.endDate = ''
            }
            if (this.params.time.length === 2) {
                this.params.startDate = this.params.time[0]
                this.params.endDate = this.params.time[1]
            }

            // if (this.params.time) {
            //     this.params.startDate = this.params.time[0]
            //     this.params.endDate = this.params.time[1]
            // }
            this.fetchpaycontentWithdrawDetail()
            this.fetchWithdrawStatistic()
        },
       fetchpaycontentWithdrawDetail(){
        //    console.log( this.username);
           this.$request.fetchpaycontentWithdrawDetail(this.params).then(res=>{
               if (res.code==200) {
                   this.formList.data = res.data.content
                   this.formList.totalCount = res.data.totalElements
               }
           })
       },
       fetchWithdrawStatistic(){
           this.$request.fetchWithdrawStatistic(this.params).then(res=>{
               if (res.code==200) {
                   this.withdrawData = res.data
               }
           })
       },
        handleSizeChange (val) {
            this.params.size = val
            this.fetchpaycontentWithdrawDetail()
        },
        handleCurrentChange (val) {
            this.params.page = val
            this.fetchpaycontentWithdrawDetail()
        }
    },
    mounted(){
        this.params.userId = this.$route.query.userId
        this.username = this.$route.query.username
        this.fetchpaycontentWithdrawDetail()
        this.fetchWithdrawStatistic()
    }
}
</script>
<style lang="scss">
    .withdrawdetail_main{
         .main_name{
                display: flex;
                padding-right: 20px;
                .sonp{
                    // white-space:nowrap;
                    // overflow:hidden;
                    text-overflow: ellipsis;
                    width: 250px;
                    cursor: pointer;
                    line-height: 34px;
                }
            }
        .qiehuan{
            .el-radio-button__inner{
                 min-width: 74px;
            }
        }
        .top{
            display: flex;
            margin-bottom: 30px;
            margin-top: 18px;
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
            .shuxian{
                font-size: 15px;
                color: #999;
                line-height: 23px;
            }
        }
    }
</style>

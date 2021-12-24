<template>
    <section class="withdraw_main">

        <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
            @handleSearch="handleSearch"
             @handleFocus='handleFocus'
		></search-header>
        <div class="top">
            <p>用户总提现(元)：<span>{{Number(withdrawStatistic.withdrawalAmount)}}</span></p>
        </div>
        <div class="balance_form">
            <base-table v-bind="formList"
            @handleDetail="handleTableDetail"
             @sortChange="handleSortChange"
             @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange">
            </base-table>
        </div>
    </section>
</template>
<script>
import searchHeader from '@/components/mixins/searchHeader'
import baseTable from '@/components/mixins/baseTable'
import {  getTime, getData, getNewTime } from '@/utils'

export default {
    name:'withdraw',
    mixins: [searchHeader,baseTable],
     props:{
        activeTab:{
            type:String,
            default:''
        }
    },
    data(){
        return{
            withdrawStatistic:{
                withdrawalAmount:0
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
                            width:'308px'
                        }
                    },
                    {
                        type: 'searchInput',
                        class: 'z-hidden-lg-and-down',
                        value: 'title',
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
                        label: '用户名',
                        prop: 'username'
                    },
                    {
                        label: '提现金额(元)',
                        prop: 'withdrawalAmount',
                        sortable: 'custom'
                    }
                ],
                handleColumn:[
                    {
                        type: 'Detail',
                        name: '详情',
                        icon: 'yulang',
                        // disabled: !this._checkPermission('/questionnaire/release', 'PUT'),
                        iconStyle: {
                        fontSize: '14px'
                        }
                    }
                ],
                handleColumnProp: {
                    label: '操作',
                    width: '100',
                    align: 'left'
                },
                showSelection: false,
                showPagination: true,
                showIndex: false,
                data: [],
                totalCount:0
            },
            list:{
                params:{
                   time2:4,
                   time:[],
                   title:'',
                   startDate: "",
                    endDate: "",
                    userName: "",
                    type: 2,
                    orderType: 4,
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
            // console.log(column,prop,order);
            let asc = 'ascending'
            let des = 'descending'
            if (prop=='withdrawalAmount'&&order==asc) {
                this.list.params.orderType = 3
            }else if (prop=='withdrawalAmount'&&order==des) {
                this.list.params.orderType = 4
            }else{
                this.list.params.orderType = 4
            }
            this.fetchEarnings()
        },
        // 搜索
		handleEventBtnSearch(item) {
			this.$request.fetchpaycontentWithdrawExport(this.list.params).then(res=>{
                this.$exportClick(res, '按提现.xlsl')
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
            this.fetchEarnings()
            this.fetchEarningsStatistic()
        },
        handleTableDetail(val){
            this.$routerLink('/config/content-pay/user-statistics/withdrawdetail','list',{userId:val.userId,username:val.username})
        },
        fetchEarnings(){
            let data = Object.assign({}, this.list.params)
            delete data.time
            delete data.time2
            this.$request.fetchEarnings(data).then(res=>{
                if (res.code==200) {
                    this.formList.data = res.data.content
                    this.floatData()
                    this.formList.totalCount = res.data.totalElements
                }
            })
        },
        floatData(){
            this.formList.data.forEach((item,index)=>{
                this.formList.data[index].withdrawalAmount = Number(item.withdrawalAmount)
            })
        },
        fetchEarningsStatistic(){
            let data = {
                title: '',
                startDate: '',
                endDate: '',
                userName: '',
                type: 2,
                orderType: '',
                page: '',
                size: '',
            }
            this.$request.fetchWithdrawStatistic(data).then(res=>{
                if (res.code==200) {
                    this.withdrawStatistic = res.data
                }
            })
        },
        handleSizeChange (val) {
            this.list.params.size = val
            this.fetchEarnings()
        },
        handleCurrentChange (val) {
            this.list.params.page = val
            this.fetchEarnings()
        }
    },
    mounted(){
        this.fetchEarnings()
    },
    watch:{
        activeTab(num){
            if (num==3) {
                this.fetchEarnings()
                this.fetchEarningsStatistic()
            }
        }
    }
}
</script>
<style lang="scss">
    .withdraw_main{
        .qiehuan{
            .el-radio-button__inner{
                 min-width: 74px;
            }
        }
        .top{
            display: flex;
            margin-bottom: 28px;
            margin-top: 12px;
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

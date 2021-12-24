<template>
    <section class="earning_main">
        
        <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
            @handleSearch="handleSearch"
            @handleFocus='handleFocus'
		></search-header>
        <div class="top">
            <p>用户总收益(元)：<span>{{Number(earningsStatistic.userTotalAmount)}}</span></p>
            <p v-if="platformOpen">平台总收益(元)：<span>{{Number(earningsStatistic.platformTotalAmount)}}</span></p>
        </div>
        <div class="balance_form">
            <base-table v-bind="formList"
             @handleIssue="handleTableIssue"
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
    name:'earnings',
    mixins: [searchHeader,baseTable],
    components: {

	},
    props:{
        activeTab:{
            type:String,
            default:''
        }
    },
    data(){
        return{
            platformOpen:0,
            earningsStatistic:{
                userTotalAmount:0,
                platformTotalAmount:0
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
                        type: 'searchInput',
                        value: 'title',
                        class: 'z-hidden-lg-and-down',
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
                        prop: 'username',
                        width:'280'
                    },
                    {
                        label: '收益(元)',
                        prop: 'totalAmount',
                        align: 'left',
                        sortable: 'custom'
                    },
                    {
                        label: '打赏(元)',
                        prop: 'rewardAmount',
                        align: 'left',
                        sortable: 'custom'
                    },
                    {
                        label: '内容付费(元)',
                        prop: 'paidAmount',
                        align: 'left',
                        sortable: 'custom'
                    }
                ],
                handleColumn:[
                    {
                        type: 'Issue',
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
                    type: 1,
                    orderType: 6,
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
         // 判断平台收益开关
        handlePlatformOpen(){
            this.$request.fetchPayConfig().then(res => {
                if (res.code==200) {
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
        handleSortChange(column, prop, order){
            console.log(column,prop,order);
            let asc = 'ascending'
            let des = 'descending'
            if (prop=='rewardAmount'&&order==asc) {
                this.list.params.orderType = 7
            }else if (prop=='rewardAmount'&&order==des) {
                this.list.params.orderType = 8
            }else if (prop=='paidAmount'&&order==asc) {
                this.list.params.orderType = 9
            }else if (prop=='paidAmount'&&order==des) {
                this.list.params.orderType = 10
            }else if (prop=='totalAmount'&&order==asc) {
                this.list.params.orderType = 5
            }else if (prop=='totalAmount'&&order==des) {
                this.list.params.orderType = 6
            }else{
                this.list.params.orderType = 6
            }
            this.fetchEarnings()
        },
        // 搜索
		handleEventBtnSearch(item) {
			 this.$request.fetchpaycontentEarningsExport(this.list.params).then(res=>{
                this.$exportClick(res, '按收益.xlsl')
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
        //    console.log(this.list.params.time);
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
            } else if (this.list.params.time&&this.list.params.time.length == 0) {
                // console.log(this.list.params);
                this.list.params.time2 = 4
                this.list.params.time = []
                this.list.params.startDate = ''
                this.list.params.endDate = ''
            } else if (!this.list.params.time) {
                // console.log(this.list.params);
                this.list.params.time2 = 4
                this.list.params.time = []
                this.list.params.startDate = ''
                this.list.params.endDate = ''
            }
            if (this.list.params.time&&this.list.params.time.length === 2) {
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
        handleTableIssue(val){
            console.log(val);
            this.$routerLink('/config/content-pay/user-statistics/detail','list',{userId:val.userId,username:val.username})
        },
        fetchpaycontentEarningsExport(){
            this.$request.fetchpaycontentEarningsExport(this.list.params).then(res=>{
                this.$exportClick(res, '按收益.xlsl')
            })
        },
        fetchEarnings(){
            let data = Object.assign({}, this.list.params)
            delete data.time
            delete data.time2
            this.$request.fetchEarnings(data).then(res=>{
                if (res.code==200) {
                    // console.log(res);
                    this.formList.data = res.data.content
                    this.floatData()
                    this.formList.totalCount = res.data.totalElements
                }
            })
        },
        floatData(){
            this.formList.data.forEach((item,index)=>{
                this.formList.data[index].paidAmount = Number(item.paidAmount)
                this.formList.data[index].rewardAmount = Number(item.rewardAmount)
                this.formList.data[index].totalAmount = Number(item.totalAmount)
            })
        },
        fetchEarningsStatistic(){
            let data = {
                title: '',
                startDate: '',
                endDate: '',
                userName: '',
                type: 1,
                orderType: '',
                page: '',
                size: '',
            }
            this.$request.fetchEarningsStatistic(data).then(res=>{
                if (res.code==200) {
                    // console.log(res);
                    this.earningsStatistic = res.data
                    this.earningsStatistic.platformTotalAmount=this.earningsStatistic.platformTotalAmount?this.earningsStatistic.platformTotalAmount:'0.0000'
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
        this.fetchEarningsStatistic()
        this.handlePlatformOpen()
    },
    watch:{
        activeTab(num){
            if (num==2) {
                this.fetchEarnings()
                this.fetchEarningsStatistic()
            }
        }
    }
}
</script>
<style lang="scss">
    .earning_main{
        .qiehuan{
            .el-radio-button__inner{
                 min-width: 74px;
            }
        }
        // .search-header-container{
        //     .el-row{
        //         .hidegaoji{
        //             display: none;
        //         }
        //     }
        // }
        // @media screen and (min-width: 1919px) {
        //     .hidegaoji {
        //         display: none !important;
        //     }
        // }
        // @media screen and (max-width: 1919px) {
        //     .z-hide-md-and-up {
        //         display: block !important;
        //     }
        // }
        .top{
            display: flex;
            margin-top: 12px;
            margin-bottom: 30px;
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
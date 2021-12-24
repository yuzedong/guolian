<template>
    <section class="balance_main">

        <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
             @handleSearch="handleSearch"
            @handleFocus='handleFocus'
		></search-header>
        <div class="top">
            <p>付费总金额(元)：<span style="font-size:20px;font-weight:700;display:inline-block;margin-right:30px;margin-left:8px;">{{qushiData.payPriceSum?Number(qushiData.payPriceSum):'0'}}</span>  <span style="font-size:14px;color:#666;font-weight:400;"><span style="font-weight:400;display:inline-block;margin-right:10px;"></span>打赏(元)：<span style="font-size:16px;font-weight:400;display:inline-block;margin-right:30px;margin-left:8px;">{{Number(qushiData.rewardSum)}}</span>  付费阅读(元)：<span style="font-size:16px;font-weight:400;display:inline-block;margin-right:30px;margin-left:8px;">{{Number(qushiData.payreadSum)}}</span>  微信支付(元)：<span style="font-size:16px;font-weight:400;display:inline-block;margin-right:30px;margin-left:8px;">{{Number(qushiData.wechatPaySum)}}</span> 支付宝支付(元)：<span style="font-size:16px;font-weight:400;display:inline-block;margin-right:10px;margin-left:8px;">{{Number(qushiData.alipayPaySum)}}</span><span style="font-weight:400;"></span></span></p>

        </div>
        <div class="one">
            <p v-if="platformOpen">平台总收益(元)：<span style="margin-left:8px;">{{Number(qushiData.platformProfitSum)}}</span></p>
            <p v-if="platformOpen" style="margin:0px 15px;line-height:23px;">|</p>
            <p>用户总收益(元)：<span style="margin-left:8px;">{{Number(qushiData.userProfitSum)}}</span></p>
        </div>
        <div class="balance_form">
            <base-table v-bind="list"
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
import qs from 'qs'

export default {
    mixins: [searchHeader,baseTable],
    data(){
        return{
            platformOpen:0,
            qushiData:{
                payPriceSum:0,
                rewardSum:0,
                payreadSum:0,
                wechatPaySum:0,
                alipayPaySum:0,
                platformProfitSum:0,
                userProfitSum:0
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
                                label: '最近7天',
                                value: 2
                            },
                            {
                                label: '最近30天',
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
                        }
                    },

                    {
						btnType: 'Search',
						type: 'henderBorderBtn',
						text: '导出明细',
						icon: 'xiangguanneirong',
						name: 'export',
                        headerClass:'exportys'
					}
                ]
            },
            formList: {

            },
            list:{
                 columns: [
                    {
                        label: '日期',
                        prop: 'statisticTime',
                        width: '140',
                        sortable: 'custom'
                    },
                    {
                        label: '付费总金额(元)',
                        prop: 'payPriceSum',
                        sortable: 'custom'
                    },
                    {
                        label: '平台总收益(元)',
                        prop: 'platformProfitSum',
                        sortable: 'custom'
                    },
                    {
                        label: '用户总收益(元)',
                        prop: 'userProfitSum',
                        sortable: 'custom'
                    },
                    {
                        label: '打赏总金额(元)',
                        prop: 'rewardSum',
                        sortable: 'custom'
                    },
                    {
                        label: '付费阅读总金额(元)',
                        prop: 'payreadSum',
                        sortable: 'custom',
                        width:'200'
                    },
                     {
                        label: '微信支付总金额(元)',
                        prop: 'wechatPaySum',
                        sortable: 'custom',
                        width:'200'
                    },
                     {
                        label: '支付宝支付总金额(元)',
                        prop: 'alipayPaySum',
                        sortable: 'custom',
                        width:'200'
                    }
                ],
                showSelection: false,
                showPagination: true,
                showIndex: false,
                data: [],
                totalCount:0,
                 pageSize: 20,

                api:'fetchpaycontentPayStatisticsDetail',
                params:{
                    beginTime:'',
                    endTime:'',
                    sortType:2,
                    page:1,
                    size:20,
                    time2:4,
                    time:[]
                },
                filterParams:['time','time2']


            }
        }
    },
    methods:{
        getTime,
        getData,
        getNewTime,
         handleFocus () {
            this.list.params.time2 = ''
            if (this.list.params.time === null) {
                this.list.params.time2 = 4
                this.list.params.time = []
                this.list.params.beginTime = ''
                this.list.params.endTime = ''
            }
        },
        handleSearch(){
            if (this.list.params.time2 !== '') {
                this.list.params.time = []
            }
        //    console.log(this.list.params.time2);
            if (this.list.params.time2) {
                if (this.list.params.time2 === 1) {
                this.list.params.beginTime = this.getData(0)
                this.list.params.endTime = this.getData(0)
                } else if (this.list.params.time2 === 2) {
                this.list.params.beginTime = this.getData(6)
                this.list.params.endTime = this.getData(0)
                } else if (this.list.params.time2 === 3) {
                this.list.params.beginTime = this.getData(29)
                this.list.params.endTime = this.getData(0)
                } else if (this.list.params.time2 === 4) {
                this.list.params.beginTime = ''
                this.list.params.endTime = ''
                }
                this.list.params.time = [this.list.params.beginTime,this.list.params.endTime]
                // this.list.params.time[0] = this.list.params.beginTime
                // this.list.params.time[1] = this.list.params.endTime
            } else if (this.list.params.time === null) {
                this.list.params.time2 = 4
                this.list.params.time = []
                this.list.params.beginTime = ''
                this.list.params.endTime = ''
            }else if (this.list.params.time.length === 2) {
                this.list.params.beginTime = this.list.params.time[0]
                this.list.params.endTime = this.list.params.time[1]
            }else{
                this.list.params.beginTime = ''
                this.list.params.endTime = ''
            }

            // if (this.list.params.time) {
            //     this.list.params.beginTime = this.list.params.time[0]
            //     this.list.params.endTime = this.list.params.time[1]
            // }
            // console.log(this.list.params.time);
            this.fetchTableApi()
            this.fetchPayStatistics()
        },
        // 导出
		handleEventBtnSearch(item) {
			let token = localStorage.getItem('JEECMS-Auth-Token')
            window.open(`${this.$path}${this.$prefix}/payTrendStatistic/exportTrendAnalysis?${qs.stringify(this.list.params)}&JEECMS-Auth-Token=${token}`)
		},
        // 排序
        handleSortChange(column, prop, order){
            let asc = 'ascending'
            let des = 'descending'
            // console.log(column,prop,order);
            if (prop=='statisticTime'&&order==asc) {
                this.list.params.sortType=1
            }else if (prop=='statisticTime'&&order==des) {
                this.list.params.sortType=2
            }else if (prop=='payPriceSum'&&order==asc) {
                this.list.params.sortType=3
            }else if (prop=='payPriceSum'&&order==des) {
                this.list.params.sortType=4
            }else if (prop=='platformProfitSum'&&order==asc) {
                this.list.params.sortType=5
            }else if (prop=='platformProfitSum'&&order==des) {
                this.list.params.sortType=6
            }else if (prop=='userProfitSum'&&order==asc) {
                this.list.params.sortType=7
            }else if (prop=='userProfitSum'&&order==des) {
                this.list.params.sortType=8
            }else if (prop=='rewardSum'&&order==asc) {
                this.list.params.sortType=9
            }else if (prop=='rewardSum'&&order==des) {
                this.list.params.sortType=10
            }else if (prop=='payreadSum'&&order==asc) {
                this.list.params.sortType=11
            }else if (prop=='payreadSum'&&order==des) {
                this.list.params.sortType=12
            }else if (prop=='wechatPaySum'&&order==asc) {
                this.list.params.sortType=13
            }else if (prop=='wechatPaySum'&&order==des) {
                this.list.params.sortType=14
            }else if (prop=='alipayPaySum'&&order==asc) {
                this.list.params.sortType=15
            }else if (prop=='alipayPaySum'&&order==des) {
                this.list.params.sortType=16
            }else{
                this.list.params.sortType=2
            }
            this.fetchTableApi()
        },
        // 获取概况数据
        fetchPayStatistics(){
            let data = {
                beginTime: this.list.params.beginTime,
                endTime: this.list.params.endTime,
                sortType: this.list.params.sortType,
                page: this.list.params.page,
                size: this.list.params.size,
            }
            this.$request.fetchPayStatistics(data).then(res=>{
                if (res.code==200) {
                    this.qushiData = res.data
                }
            })
        },
        // 获取表格数据
        fetchTableCallBack(res){
                if (res.code==200) {
                    this.list.data = res.data.content
                    this.list.totalCount = res.data.totalElements
                    this.list.data.forEach((item,index)=>{
                        item.alipayPaySum = Number(item.alipayPaySum)
                        item.payPriceSum = Number(item.payPriceSum)
                        item.payreadSum = Number(item.payreadSum)
                        item.platformProfitSum = Number(item.platformProfitSum)
                        item.rewardSum = Number(item.rewardSum)
                        item.userProfitSum = Number(item.userProfitSum)
                        item.wechatPaySum = Number(item.wechatPaySum)
                    })
                }
        },
        handleSizeChange (val) {
            this.list.params.size = val
            this.fetchTableApi()
        },
        handleCurrentChange (val) {
            this.list.params.page = val
            this.fetchTableApi()
        },
          // 获取平台总收益是否开启
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
                    // console.log(this.platformOpen);
                }
            })
        },
    },
    mounted(){
        this.fetchTableApi()
        this.fetchPayStatistics()
        this.handlePlatformOpen()
    }
}
</script>
<style lang="scss">
    .balance_main{
        .exportys{
            height: 32px;
        }
        .qiehuan{
             .el-radio-button__inner{
                 min-width: 100px;
            }
        }
        .top{
            display: flex;
            margin-bottom: 30px;
            margin-top: 18px;
            p{
                margin-right: 50px;
                color: #666;
                font-size: 14px;
                font-family: pingfang;
                font-weight: 400;
                span{
                    font-family: pingfang;
                    color: #333;
                }
            }
        }
        .one{
            display: flex;
            margin-bottom: 30px;
             p{
                color: #666;
                font-size: 14px;
                font-weight: 400;
                span{
                    color: #333;
                    font-size: 20px;
                    font-weight: 700;
                }
            }
        }
    }
</style>

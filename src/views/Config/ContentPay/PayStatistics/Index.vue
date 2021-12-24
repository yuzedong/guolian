<template>
    <section class="pay_statistics_main">
        <section class="top_pay">
            <p class="pay_p">付费概况</p>
            <div class="today_liulang">

                <ul class="content_today">
                    <li>付费总金额(元)</li>
                    <li><span>今日</span><span style="color:#DF2F4D">{{Number(todayData.payPriceSum)}}</span></li>
                    <li><span>昨天</span><span class="yesterday-wrap">{{Number(yestodayData.payPriceSum)}}</span></li>
                    <!-- <li><span>历史最高</span><span>123</span></li> -->
                    <li><span>累计</span><span>{{Number(sumData.payPriceSum)}}</span></li>
                </ul>
                 <ul v-if="platformOpen">
                    <li>平台总收益(元)</li>
                    <li><span>今日</span><span style="color:#1E84E4">{{Number(todayData.platformProfitSum)}}</span></li>
                    <li><span>昨天</span><span class="yesterday-wrap">{{Number(yestodayData.platformProfitSum)}}</span></li>
                    <!-- <li><span>历史最高</span><span>456</span></li> -->
                    <li><span>累计</span><span>{{Number(sumData.platformProfitSum)}}</span></li>
                </ul>
                <ul>
                    <li>用户总收益(元)</li>
                    <li><span>今日</span><span style="color:#E7B40E">{{Number(todayData.userProfitSum)}}</span></li>
                    <li><span>昨天</span><span class="yesterday-wrap">{{Number(yestodayData.userProfitSum)}}</span></li>
                    <!-- <li><span>历史最高</span><span>456</span></li> -->
                    <li><span>累计</span><span>{{Number(sumData.userProfitSum)}}</span></li>
                </ul>
               <ul>
                    <li>打赏总金额(元)</li>
                    <li><span>今日</span><span style="color:#1EBBBA">{{Number(todayData.rewardSum)}}</span></li>
                    <li><span>昨天</span><span class="yesterday-wrap">{{Number(yestodayData.rewardSum)}}</span></li>
                    <!-- <li><span>历史最高</span><span>456</span></li> -->
                    <li><span>累计</span><span>{{Number(sumData.rewardSum)}}</span></li>
                </ul>
                 <ul>
                    <li>付费阅读总金额(元)</li>
                    <li><span>今日</span><span style="color:#38C060">{{Number(todayData.payreadSum)}}</span></li>
                    <li><span>昨天</span><span class="yesterday-wrap">{{Number(yestodayData.payreadSum)}}</span></li>
                    <!-- <li><span>历史最高</span><span>456</span></li> -->
                    <li><span>累计</span><span>{{Number(sumData.payreadSum)}}</span></li>
                </ul>
            </div>
        </section>
        <search-header
        :params="list.params"
        v-bind="searchHeaders"
        @handleSearch="handleSearch"
        @handleFocus='handleFocus'
        ></search-header>
        <section class="vchart">
             <div class="title">
                <h3>趋势分析</h3>

                <p class="left jee-color" @click="toDetail">查看更多</p>
            </div>
            <div class="chart_main">
                <tendency :tendencyFields='tendencyFields' :tendencyData='finallyData' :selected='tabNum' ></tendency>
            </div>
        </section>
        <section class="bottom_form">
            <div class="source statistics-box">
                <div class="title">
                    <span>用户Top10</span>
                    <span class="jee-color" @click="gotodetail" style="margin-top:1px;">查看更多</span>
                </div>
                <base-table v-bind="sourceList"
                @sortChange="handleSortChangesource"
                >
                </base-table>
            </div>
            <div class="entrance statistics-box">
                <div class="title">
                <span>内容Top10</span>
                <span class="jee-color" @click="gotocontentdetail" style="margin-top:1px;">查看更多</span>
                </div>
                <base-table v-bind="entranceList"
                 @sortChange="handleSortChangeentrance"
                 class="tableLittle">
                    <template #contentTitle="scope">
                        <el-tooltip :content="scope.scope.row.contentTitle" placement="bottom" effect="light">
                            <p :class="scope.scope.row.deleteFlag?'huisep':'bluep'"  @click="showPreivew(scope.scope.row.deleteFlag,scope.scope.row.url)" >[{{scope.scope.row.channelName}}] <span>{{scope.scope.row.contentTitle}}</span></p>
                        </el-tooltip>
                        <!-- <p class="hideTitle" >[{{scope.scope.row.channelName}}]<span>{{scope.scope.row.contentTitle}}</span></p> -->

                    </template>
                </base-table>
            </div>
        </section>
    </section>
</template>
<script >
import searchHeader from '@/components/mixins/searchHeader'
import baseTable from '@/components/mixins/baseTable'
import tendency from './Tendency'
import {  getTime, getData, getNewTime } from '@/utils'

import {
  mapActions,
  mapGetters
} from 'vuex'
const DataSet = require('@antv/data-set')

export default ({
    components:{
        tendency
    },
    name: 'configContentPay',
    mixins: [searchHeader,baseTable],
    data(){
        return{
            platformOpen:0,
            todayData:{
                payPriceSum:0,
                payreadSum:0,
                platformProfitSum:0,
                rewardSum:0,
                statisticTime:0,
                userProfitSum:0,
                wechatPaySum:0
            },
            yestodayData:{
                payPriceSum:0,
                payreadSum:0,
                platformProfitSum:0,
                rewardSum:0,
                statisticTime:0,
                userProfitSum:0,
                wechatPaySum:0
            },
            sumData:{
                payPriceSum:0,
                payreadSum:0,
                platformProfitSum:0,
                rewardSum:0,
                userProfitSum:0
            },
            searchHeaders: {
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
                    type: 'dataPicker',
                    rangeText: '-',
                    value: 'time',
                    label: '选择日期：',
                    pickerOptions: {
                        disabledDate (time) {
                            return (time.getTime() > Date.now() - 8.64e6)||(time.getTime() < Date.now() - 8.64e6*3660)
                        }
                    }
                }
                ]
            },
            sourceList: {
                columns: [
                    {
                        label: '用户名',
                        prop: 'username'
                    },
                    {
                        label: '累计收益(元)',
                        prop: 'totalAmount',
                        sortable: 'custom'
                    },
                    {
                        label: '打赏(元)',
                        prop: 'rewardAmount',
                        sortable: 'custom'
                    },
                    {
                        label: '付费阅读(元)',
                        prop: 'paidAmount',
                        sortable: 'custom'
                    }
                ],
                showSelection: false,
                showPagination: false,
                showIndex: true,
                data: []
            },
            entranceList: {
                 columns: [
                    {
                        label: '内容标题',
                        prop: 'contentTitle',
                        scopeType:'slot'
                    },
                    {
                        label: '累计收益(元)',
                        prop: 'totalAmountInitial',
                        sortable: 'custom',
                        align:'left'
                    },
                    {
                        label: '打赏(元)',
                        prop: 'rewardAmountInitial',
                        sortable: 'custom',
                        align:'left'
                    },
                    {
                        label: '付费阅读(元)',
                        prop:'paidAmountInitial',
                        sortable:'custom',
                        align:'left'
                    }
                ],
                showSelection: false,
                showPagination: false,
                showIndex: true,
                data: []
            },
            tendencyFields: ['payPriceSum','payreadSum','rewardSum'],
            finallyData:[],
            sourceData: [
                {
                    '金额':0,
                    pv:0,
                    sum:0,
                    time:'2020-01-20'
                },
                {
                    pv:1,
                    '金额':2,
                    sum:3,
                    time:'2020-5-21'
                }
            ],
            userTop:{
                sortType:2,
            },
            contentTop:{
                sortType:4,
            },
            list:{
                api:'fetchpaycontentPayStatisticsVchart',
                params:{
                    beginTime:'',
                    endTime:'',
                    time:'',
                    time2:4
                },
                filterParams:['time','time2']
            },
            tabNum:4
        }
    },
    computed:{
    ...mapGetters('app', ['chartOption', 'axisOption', 'legendOption', 'colorOption'])
    },
    methods:{
        getTime,
        getData,
        getNewTime,
         // 跳转预览页
        showPreivew(flag,url){
            if (flag) {
                this.$confirm('内容已删除，无法预览', '提示', {
                    confirmButtonText: '关闭',
                    type: 'warning',
                    showCancelButton:false
                }).then((res) => {}).catch(() => {})
            }else{
                window.open(url)
            }
        },
         // 获取累计数据
        fetchPayStatistics(){
            this.$request.fetchPayStatistics(this.list.params).then(res=>{
                if (res.code==200) {
                    this.sumData = res.data
                }
            })
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
         // 搜索
         handleFocus () {
            this.list.params.time2 = ''
            if (this.list.params.time === null) {
                this.list.params.time2 = ''
                this.list.params.time = []
                this.list.params.beginTime = ''
                this.list.params.endTime = ''
            }
        },
        handleSearch(){
            if (this.list.params.time2 !== '') {
                this.list.params.time = []
            }

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
                this.list.params.time[0] = this.list.params.beginTime
                this.list.params.time[1] = this.list.params.endTime
            } else if (this.list.params.time === null) {
                this.list.params.time2 = ''
                this.list.params.time = []
                this.list.params.beginTime = ''
                this.list.params.endTime = ''
            }
            if (this.list.params.time.length === 2) {
                this.list.params.beginTime = this.list.params.time[0]
                this.list.params.endTime = this.list.params.time[1]
            }else{
                this.list.params.beginTime = ''
                this.list.params.endTime = ''
            }
            // this.list.params.beginTime = `${this.list.params.beginTime} 00:00:00`
            // this.list.params.endTime =  `${this.list.params.endTime} 00:00:00`
            this.tabNum = this.list.params.time2
            if ( this.list.params.beginTime ==  this.list.params.endTime) {
                this.tabNum = 1
            }
            // console.log(this.list.params.time);
            this.fetchStatisticsImage()
        },
        getTendencyData () {
            let dv = new DataSet.View().source(this.sourceData)
            dv.transform({
                type: 'fold',
                fields: ['pv', '金额','sum'],
                key: 'city',
                value: 'temperature'
            })
            this.tendencyData = dv.rows
        },
         // 趋势图表
    fetchStatisticsImage () {
        // delete this.list.params.time
        // delete this.list.params.time2
        this.tendencyFields[0] = '付费总金额'
        this.tendencyFields[1] = '打赏'
        this.tendencyFields[2] = '付费阅读'
        this.fetchTableApi()

    },
    fetchTableCallBack(res){
        if (res.code === 200) {
            this.finallyData = res.data.map((v,ind) =>{
                return {
                    hours:v.hours,
                    '付费总金额':v.payPriceSum?Number(v.payPriceSum):0,
                    '打赏':v.payreadSum?Number(v.payreadSum):0,
                    '付费阅读':v.rewardSum?Number(v.rewardSum):0,
                    statisticTime:v.statisticTime,
                }
            })
            // console.log(this.finallyData);
        }
    },

        gotodetail(){
            this.$routerLink('/config/content-pay/user-statistics/index')
        },
        toDetail(){
            this.$routerLink('/config/content-pay/pay-statistics/detail')
        },
        gotocontentdetail(){
            this.$routerLink('/config/content-pay/pay-detail','list')
        },
        // 获取付费概况数据
        fetchpaycontentPaystatisticstop(){
            this.$request.fetchpaycontentPaystatisticstop().then(res=>{
                if (res.code==200) {
                    if(res.data[0]){
                        this.todayData = res.data[0]
                    }
                    if (res.data[1]) {
                        this.yestodayData = res.data[1]
                    }
                }
            })
        },
        // 用户top10数据
        fetchPayStatisticsUserTop(){
            this.$request.fetchPayStatisticsUserTop(this.userTop).then(res=>{
                if (res.code==200) {
                    this.sourceList.data = res.data
                     this.floatData()
                }
            })
        },
        floatData(){
            this.sourceList.data.forEach((item,index)=>{
                this.sourceList.data[index].rewardAmount = Number(item.rewardAmount)
                this.sourceList.data[index].totalAmount = Number(item.totalAmount)
                this.sourceList.data[index].paidAmount = Number(item.paidAmount)
            })
        },
        // 用户top10数据排序
        handleSortChangesource(column, prop, order){
            //  console.log(column,prop,order);
            let asc = 'ascending'
            let des = 'descending'
            if (prop=="totalAmount"&&order==asc) {
                this.userTop.sortType = 1
            }else if (prop=="totalAmount"&&order==des) {
                this.userTop.sortType = 2
            }else if (prop=="rewardAmount"&&order==asc) {
                this.userTop.sortType = 3
            }else if (prop=="rewardAmount"&&order==des) {
                this.userTop.sortType = 4
            }else if (prop=="paidAmount"&&order==asc) {
                this.userTop.sortType = 5
            }else if (prop=="paidAmount"&&order==des) {
                this.userTop.sortType = 6
            }else{
               this.userTop.sortType = 2
            }
            this.fetchPayStatisticsUserTop()
        },
        // 内容top10数据
        fetchPayStatisticsContentTop(){
            this.$request.fetchPayStatisticsContentTop(this.contentTop).then(res=>{
                if (res.code==200) {
                    this.entranceList.data = res.data
                    this.floatData1()
                }
            })
        },
         floatData1(){
            this.entranceList.data.forEach((item,index)=>{
                this.entranceList.data[index].rewardAmountInitial = Number(item.rewardAmountInitial)
                this.entranceList.data[index].totalAmountInitial = Number(item.totalAmountInitial)
                this.entranceList.data[index].paidAmountInitial = Number(item.paidAmountInitial)
            })
        },
         // 内容top10数据排序
        handleSortChangeentrance(column, prop, order){
            //  console.log(column,prop,order);
            let asc = 'ascending'
            let des = 'descending'
            if (prop=="totalAmountInitial"&&order==asc) {
                this.contentTop.sortType = 3
            }else if (prop=="totalAmountInitial"&&order==des) {
                this.contentTop.sortType = 4
            }else if (prop=="rewardAmountInitial"&&order==asc) {
                this.contentTop.sortType = 5
            }else if (prop=="rewardAmountInitial"&&order==des) {
                this.contentTop.sortType = 6
            }else if (prop=="paidAmountInitial"&&order==asc) {
                this.contentTop.sortType = 7
            }else if (prop=="paidAmountInitial"&&order==des) {
                this.contentTop.sortType = 8
            }else{
                this.contentTop.sortType = 4
            }
            this.fetchPayStatisticsContentTop()
        },
    },
    mounted(){
        this.getTendencyData()
        this.fetchStatisticsImage()
        this.fetchPayStatisticsUserTop()
        this.fetchPayStatisticsContentTop()
        this.fetchpaycontentPaystatisticstop()
        this.handlePlatformOpen()
        this.fetchPayStatistics()
    }

})
</script>
<style lang='scss'>
    .pay_statistics_main{
        .qiehuan{
            .el-radio-button__inner{
                 min-width: 100px;
            }
        }
        .hideTitle{
            height: 24px;
            width: 150px;
            white-space:nowrap;
            overflow:hidden;
            text-overflow: ellipsis;

        }
        .bluep{
            width: 150px;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
            cursor: pointer;
            color: #666;
            span{
                color:#1EC6DF;
            }
        }
        .huisep{
            width: 150px;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
            cursor: pointer;
            color: #ccc;
            span{
                color:#ccc;
            }
        }
        .bottom_form{
            margin-top: 30px;
            display: flex;
            justify-content: space-between;
            .source{
                width: 49%;
                height: 610px;
            }
            .entrance{
                width: 49%;
                height: 610px;
                .tableLittle{
                    margin-bottom: 30px;
                }
            }
             .statistics-box{
                box-sizing: border-box;
                padding: 30px;
                border:1px solid rgba(232,232,232,1);
                border-radius: 4px;
                margin-bottom: 30px;
                .title{
                display: flex;
                justify-content:space-between;
                margin-bottom: 30px;
                    span:first-child{
                        font-size: 18px;
                        color: #333333;
                        font-weight: bold;
                    }
                    span:last-child{
                        font-size: 14px;
                        cursor: pointer;
                    }
                }
            }
        }
        .vchart{
            height: 400px;
            border:1px solid rgba(232,232,232,1);
            border-radius:4px;
            box-sizing: border-box;
            padding: 30px 29px;
            .title{
                display: flex;
                line-height: 18px;
                position: relative;
                margin-bottom: 30px;
                h3{
                font-size: 18px;
                color: #333333;
                margin-right: 29px;
                }
                .left{
                    position: absolute;
                    top: 0;
                    right: 0;
                    cursor: pointer;
                }
                .green{
                    display: inline-block;
                    width: 10px;
                    height: 10px;
                    border-radius: 50%;
                    background: green;
                    margin-right: 10px;
                    margin-top: 3px;
                }
                .blue{
                    display: inline-block;
                    width: 10px;
                    height: 10px;
                    border-radius: 50%;
                    background: blue;
                    margin:0px 10px;
                    margin-top: 3px;

                }
                .yellow{
                    display: inline-block;
                    width: 10px;
                    height: 10px;
                    border-radius: 50%;
                    background: yellow;
                    margin:0px 10px;
                    margin-top: 3px;
                }
            }
        }
        .top_pay{
            border: 1px solid rgba(232,232,232,1);
            border-radius:4px;
            box-sizing: border-box;
            padding: 30px;
            margin-bottom: 30px;
            .pay_p{
                font-size: 18px;
                font-family: Microsoft YaHei;
                font-weight: bold;
                color: #333333;
            }
            .today_liulang{
                display: flex;
                justify-content:space-between;
                .content_today{
                    margin-left: 0!important;
                }
                ul{
                flex: 6;
                margin-left: 29px;
                font-size: 14px;
                li:first-child{
                    text-align: right;
                    display: block;
                    border-right:none;
                    font-size: 16px;
                    color: #333333;
                    position: relative;
                    padding-bottom: 34px;
                    padding-top: 15px;
                    .jee-popover{
                    margin-left: 16px;
                    right: 0;
                    margin-right: 0;
                    }
                }
                li:nth-child(2){
                    padding-top: 0;
                }
                li:last-child{
                    padding-bottom: 0;
                }
                li{
                    line-height: 12px;
                    display: flex;
                    padding: 15px 0;
                    justify-content:space-between;
                    border-right: 1px dashed rgba(232,232,232,1);
                    padding-right: 29px;
                    color: #666666;
                    span:last-child{
                    font-size: 18px;
                    color: #999999;
                    }
                }
                li:nth-child(3){
                    span:last-child{
                    color: #333333;
                    }
                }
                li:nth-child(2){
                    span:last-child{
                    font-size: 24px;
                    }
                }
                .yesterday-wrap{
                    position: relative;
                    .jee-svg-icon{
                    position: absolute;
                    right: -18px;
                    top: 15px;
                    }
                }
                }
                ul:last-child{
                    li{
                        border: none;
                    }
                }

            }
        }
    }
</style>

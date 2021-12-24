<template>
    <section class="detail_main">
        <h3 style="margin-bottom:30px;">用户名：{{userName}}</h3>

         <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="params"
			@handleBtnSearch="handleEventBtnSearch"
            @handleSearch="handleSearch"
            @handleFocus='handleFocus'
		></search-header>
        <div class="top">
            <p>总金额(元)：<span>{{Number(statisticsData.userTotalAmount)}}</span></p>
            <p class="shu">|</p>
            <p>打赏(元)：<span>{{Number(statisticsData.rewardAmount)}}</span></p>
            <p class="shu">|</p>
            <p>付费阅读(元)：<span>{{Number(statisticsData.payContentAmount)}}</span></p>
        </div>
        <div class="balance_form">
            <base-table v-bind="formList"
            @sortChange="handleSortChange"
            @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange">
                <template #type="scope">
                    <p>{{scope.scope.row.type==1?'付费阅读':scope.scope.row.type==2?'打赏':''}}</p>
                </template>
                <template #payType="scope">
                    <p>{{scope.scope.row.payType==1?'支付宝':scope.scope.row.payType==2?'微信':scope.scope.row.payType==101?'余额':''}}</p>
                </template>
                <template #amountStr="scope">
                    <el-popover
                    placement="top-start"
                    width="120"
                    trigger="hover"
                    >
                        <p>支付总金额：{{Number(scope.scope.row.payAmount)}} <span v-if="platformOpen">，用户所得金额：{{Number(scope.scope.row.userAmount)}}，平台抽成金额：{{Number(scope.scope.row.rakeAmount)}}</span></p>
                        <p slot="reference">{{Number(scope.scope.row.payAmount)}} <span v-if="platformOpen">({{Number(scope.scope.row.userAmount)}}|{{Number(scope.scope.row.rakeAmount)}})</span></p>
                    </el-popover>
                </template>
                <template #contentTitle="scope">
                    <el-tooltip :content="scope.scope.row.contentTitle" placement="bottom" effect="light">
                         <p :class="scope.scope.row.deleteFlag?'huise':'clickP'" @click="preview(scope.scope.row.deleteFlag,scope.scope.row.url)">[{{scope.scope.row.channelName}}]<span style="color:#1EC6DF;">{{scope.scope.row.contentTitle}}</span></p>
                    </el-tooltip>

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
    name:'configUserStatisticsDetail',
    mixins: [searchHeader,baseTable],
    data(){
        return{
            platformOpen:false,
            userName:'',
            statisticsData:{
                payContentAmount:0,
                rewardAmount:0,
                userTotalAmount:0
            },
            searchHeader: {
                searchItems: [
                    {
                        type: 'cutButton',
                        value: 'time2',
                        label: '时间:',
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
                                return (time.getTime() > Date.now() - 8.64e6)||(time.getTime() < Date.now() - 8.64e6*3660)
                            }
                        },
                        style:{
                            width:'288px'
                        }
                    },
                    {
                        type: 'select',
                        label: '支付方式',
                        value: 'payType',
                        class: 'z-hidden-lg-and-down',
                        style: {
                        width: '150px'
                        },
                        options:[
                            {
                                label: '余额',
                                value: 101
                            },
                            {
                                label: '微信支付',
                                value: 2
                            },
                            {
                                label: '支付宝支付',
                                value: 1
                            },
                            {
                                label: '全部',
                                value: ''
                            }
                        ]
                    },
                    {
                        type: 'select',
                        label: '收益类型',
                        value: 'type',
                        class: 'z-hidden-lg-and-down',
                        style: {
                        width: '150px'
                        },
                        options:[
                            {
                                label: '打赏',
                                value: 2
                            },
                            {
                                label: '付费阅读',
                                value: 1
                            },
                            {
                                label: '全部',
                                value: ''
                            }
                        ]
                    },
                    {
                        type: 'select',
                        label: '',
                        value: 'nameType',
                        hiddenKey: 'senior',
                        hiddenValue: true,
                        style: {
                        width: '120px'
                        },
                        options:[
                            {
                                label: '支付用户',
                                value: 1
                            },
                            {
                                label: '支付账号',
                                value: 2
                            },
                            {
                                label: '订单号',
                                value: 3
                            }
                        ]
                    },
                    {
                        type: 'searchInput',
                        value: 'name',
                        placeholder: '',
                        hiddenKey: 'senior',
                        hiddenValue: true,
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
                    }
                ]
            },
            formList: {
                api:'fetchpaycontentEarningsDetail',
                 columns: [
                    {
                        label: '付费类型',
                        prop: 'type',
                        width:"140",
                        scopeType:'slot',
                        align:'left'
                    },
                    {
                        label: '金额(元)',
                        prop: 'amountStr',
                         align: 'left',
                        sortable: 'custom',
                        scopeType:'slot'
                    },
                    {
                        label: '支付方式(元)',
                        prop: 'payType',
                        align: 'left',
                        scopeType:'slot'
                    },
                     {
                        label: '支付时间',
                        prop: 'payTime',
                         align: 'left',
                        sortable: 'custom'
                    },
                     {
                        label: '支付用户及IP',
                        prop: 'payUserNameAndIp',
                        align: 'left',
                    },
                     {
                        label: '订单号',
                        prop: 'orderCode',
                         align: 'left'
                    },
                     {
                        label: '内容标题',
                        prop: 'contentTitle',
                        align: 'left',
                        scopeType:'slot',
                        width:'300'
                    },
                     {
                        label: '内容ID',
                        prop: 'contentId',
                         align: 'left',
                         width:'100'
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
                name:'',
                nameType:1,
                payType:'',
                type:'',
                orderType:4,
                userId:'',
                time:[],
                time2:4,
                page:1,
                size:10
            },
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
        // 判断文章是否删除
        preview(openOff,url){
            //  console.log(this.$route.query.url);
            if (openOff) {
                this.$confirm('内容已删除，无法预览', '提示', {
                    confirmButtonText: '关闭',
                    type: 'warning',
                    showCancelButton:false
                }).then((res) => {}).catch(() => {})
            }else{
                window.open(url)
            }
        },
          // 排序
        handleSortChange(column, prop, order){
            let asc = 'ascending'
            let des = 'descending'
            if (prop=='amountStr'&&order==asc) {
                this.params.orderType = 1
            }else if (prop=='amountStr'&&order==des) {
                this.params.orderType = 2
            }else if (prop=='payTime'&&order==asc) {
                this.params.orderType = 3
            }else if (prop=='payTime'&&order==des) {
                this.params.orderType = 4
            }else{
               this.params.orderType = 4
            }
            this.fetchpaycontentEarningsDetail()
        },
        // 搜索
		handleEventBtnSearch(item) {
			this.$request.fetchpaycontentEarningsDetailExport(this.params).then(res=>{
                this.$exportClick(res, '按收益明细.xlsl')
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
            if (this.params.time2 !== '') {
                this.params.time = []
            }
            // console.log(this.params.time);
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
            } else if (this.params.time === 'null') {
                this.params.time2 = 1
                this.params.time = []
                this.params.startDate = this.getData(0)
                this.params.endDate = this.getData(0)
            }else if (this.params.time&&this.params.time.length==0) {
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
            this.list.params.page = 1
            this.fetchpaycontentEarningsDetail()
            this.fetchpaycontentEarningsStatisticDetail()
        },

        fetchpaycontentEarningsStatisticDetail(){
            this.$request.fetchpaycontentEarningsStatisticDetail(this.params).then(res=>{
                if (res.code==200) {
                    this.statisticsData = res.data
                }
            })
        },
        fetchpaycontentEarningsDetail(){
            delete this.params.time
            delete this.params.time2
            // console.log(this.$route.query.type.userName,this.params.userId);
            this.$request.fetchpaycontentEarningsDetail(this.params).then(res=>{
                if (res.code==200) {
                    // console.log(res);
                    this.formList.data = res.data.content
                    this.formList.totalCount = res.data.totalElements
                    this.params.size = res.data.size
                }
            })
        },
        handleSizeChange (val) {
            console.log(val);
            this.params.size = val
            this.fetchpaycontentEarningsDetail()
        },
        handleCurrentChange (val) {
            this.params.page = val
            this.fetchpaycontentEarningsDetail()
        }
    },
    mounted(){
        this.userName = this.$route.query.username
        this.params.userId = Number(this.$route.query.userId)
        this.fetchpaycontentEarningsDetail()
        this.fetchpaycontentEarningsStatisticDetail()
        this.handlePlatformOpen()

    },
    activated(){
         this.userName = this.$route.query.username
        this.params.userId = Number(this.$route.query.userId)
        this.fetchpaycontentEarningsDetail()
        this.fetchpaycontentEarningsStatisticDetail()
        this.handlePlatformOpen()
    }
}
</script>
<style lang="scss">
    .detail_main{
        .clickP{
            cursor: pointer;
            width: 220px;
            white-space:nowrap;
            overflow:hidden;
            text-overflow: ellipsis;
            color: #666;
        }
        .huise{
            cursor: pointer;
            width: 220px;
            white-space:nowrap;
            overflow:hidden;
            text-overflow: ellipsis;
            color: #ccc;
            span{
                color: #ccc !important;
            }
        }
        .phide{
            width: 220px;
            color: #ccc;
            span{
                color: #ccc !important;
            }
        }
        .top{
            display: flex;
            margin-top: 18px;
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

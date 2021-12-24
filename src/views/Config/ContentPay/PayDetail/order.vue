<template>
    <section class="order_main">

         <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
            @handleFocus='handleFocus'
            @handleSearch="handleSearch"
		></search-header>
        <div class="top">
            <p>总金额(元)：<span class="touone">{{Number(orderData.sum)}}</span> &nbsp;&nbsp; <span style="font-size:14px;color:#999;"><span style="margin-right:30px;">打赏(元)：{{Number(orderData.reward)}}</span><span style="margin-right:30px;"> 付费阅读(元)：{{Number(orderData.pay)}}</span> <span style="margin-right:30px;">微信支付(元)：{{Number(orderData.wechat)}}</span> <span>支付宝支付(元)：{{Number(orderData.alipay)}}</span></span></p>

        </div>
        <div class="shouyi">
            <p>平台总收益(元)：<span>{{Number(orderData.sumPlatformIncome)}}</span></p>
            <p style="line-height:23px;">|</p>
            <p>用户总收益(元)：<span>{{Number(orderData.sumUserIncome)}}</span></p>
        </div>
        <div class="balance_form">
            <base-table v-bind="formList"
             @sortChange="handleSortChange"
             @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange"
            >
                <template #payType="scope">
                    <p>{{scope.scope.row.payType==1?'支付宝':scope.scope.row.payType==2?'微信':scope.scope.row.payType==101?'余额':''}}</p>
                </template>
                <template #type="scope">
                    <p>{{scope.scope.row.type==1?'付费阅读':scope.scope.row.type==2?'打赏':''}}</p>
                </template>
                <template #title="scope">
                    <el-tooltip v-if="scope.scope.row.title.length>18" :content="scope.scope.row.title" placement="bottom" effect="light">
                         <p class="z-width-md" :class="scope.scope.row.deletedFlag?'huisep':'bluep'"  @click="showPreivew(scope.scope.row.deletedFlag,scope.scope.row.contentId,scope.scope.row.contentUrl)">[{{scope.scope.row.channelName}}] <span>{{scope.scope.row.title}}</span></p>
                    </el-tooltip>
                    <p v-else class="z-width-md" :class="scope.scope.row.deletedFlag?'huisep':'bluep'"  @click="showPreivew(scope.scope.row.deletedFlag,scope.scope.row.contentId,scope.scope.row.contentUrl)">[{{scope.scope.row.channelName}}] <span>{{scope.scope.row.title}}</span></p>

                </template>
                <template #amount="scope">
                     <el-popover
                        placement="top-start"
                        width="200"
                        trigger="hover"
                        >
                        <p>总金额：{{Number(scope.scope.row.realAmount)}} <span v-if="platformOpen">，平台佣金：{{Number(scope.scope.row.rakePrice)}}，用户实际收益：{{Number(scope.scope.row.actualIncome)}}</span></p>
                         <p slot="reference">{{Number(scope.scope.row.realAmount)}} <span v-if="platformOpen">({{Number(scope.scope.row.actualIncome)}}|{{Number(scope.scope.row.rakePrice)}})</span></p>
                    </el-popover>
                </template>
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
    name:'order',
    mixins: [searchHeader,baseTable],
    props:{
        activeTab:{
            type:String,
            default:''
        }
    },
    data(){
        return{
            platformOpen:false,
            orderData:{
                alipay:0,
                pay:0,
                reward:0,
                sum:0,
                sumPlatformIncome:0,
                sumUserIncome:0,
                wechat:0
            },
            searchHeader: {
                searchItems: [
                    {
                        type: 'cutButton',
                        value: 'time2',
                         tabClass:'qiehuan',
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
                                return time.getTime() > Date.now() - 8.64e6
                            }
                        },
                        style:{
                            width:'318px'
                        }
                    },
                    {
                        type: 'cascader',
                        value: 'ids',
                        props: {
                        label: 'name',
                        value: 'id',
                        checkStrictly: true
                        },
                        style: {
                            width: '163px'
                        },
                        label: '选择栏目:',
                        placeholder: '请选择',
                        hiddenKey: 'senior',
                        hiddenValue: true,
                        options: [

                        ]
                    },
                     {
                        type: 'cascader',
                        value: 'sites',
                        hiddenKey: 'senior',
                        hiddenValue: true,
                        props: {
                            label: 'name',
                            value: 'id',
                            checkStrictly: true
                        },
                        style: {
                            width: '163px'
                        },
                        label: '选择站点:',
                        placeholder: '请选择',
                        options: [
                            {
                                name: '全部',
                                id: ''
                            }
                        ]
                    },

                     {
                        type: 'select',
                        label: '支付方式:',
                        hiddenKey: 'senior',
                        hiddenValue: true,
                        value: 'payType',
                        style: {
                        width: '120px'
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
                        label: '付费类型:',
                        hiddenKey: 'senior',
                        hiddenValue: true,
                        value: 'type',
                        style: {
                        width: '120px'
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
                        value: 'keyType',
                        hiddenKey: 'senior',
                        hiddenValue: true,
                        style: {
                        width: '120px'
                        },
                        options:[
                            {
                                label: '内容标题',
                                value: 1
                            },
                            {
                                label: '内容ID',
                                value: 2
                            },
                            {
                                label: '创建人',
                                value: 5
                            },
                            {
                                label: '支付用户',
                                value: 3
                            },
                            {
                                label: '订单号',
                                value: 4
                            }
                        ]
                    },
                    {
                        type: 'searchInput',
                        value: 'key',
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
                        value: 'senior'
                    }
                ]
            },
            formList: {
                 columns: [
                    {
                        label: '付费类型',
                        prop: 'type',
                        scopeType:'slot',
                        maxWidth:'120'
                    },
                    {
                        label: '金额(元)',
                        prop: 'amount',
                        sortable: 'custom',
                        scopeType:"slot",
                        width:'150'
                    },
                    {
                        label: '支付方式(元)',
                        prop: 'payType',
                        scopeType:'slot',
                        width:'120'
                    },
                     {
                        label: '支付时间',
                        prop: 'payTime',
                        sortable: 'custom',
                        maxWidth:'175'
                    },
                     {
                        label: '支付用户及IP',
                        prop: 'addressAndIpAndUsername',
                        maxWidth:'200'
                    },
                     {
                        label: '订单号',
                        prop: 'code',
                        maxWidth:'200'
                    },
                     {
                        label: '内容标题',
                        prop: 'title',
                        scopeType:'slot',
                        maxWidth:'400',
                        minWidth:'120'
                    },
                     {
                        label: '内容ID',
                        prop: 'contentId',
                        width:'75'
                    },
                    {
                        label: '创建人',
                        prop: 'createUser',
                         align: 'right',
                         width:'100'
                    }
                ],

                showSelection: false,
                showPagination: true,
                showIndex: false,
                totalCount:0,
                data: []
            },
            list:{
                api:'fetchpaycontentPaydetailOrder',
                params:{
                    time:'',
                    time2:4,
                    payStartTime:'',
                    payEndTime:'',
                    // channelIds:'',
                    payType:'',
                    type:'',
                    key:'',
                    keyType:1,
                    orderBy:3,
                    page:1,
                    size:10,
                    // numlength:0,
                    first:'',
                    second:'',
                    ids:[''],
                    sites:[1],
                    siteId:1
                },
                filterParams:['time','time2','ids','sites']
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
        showPreivew(openoff,id,url){
            if (openoff) {
                this.$confirm('内容已删除，无法预览', '提示', {
                    confirmButtonText: '关闭',
                    type: 'warning',
                    showCancelButton:false
                }).then((res) => {}).catch(() => {})
            }else{
                // console.log(url);
                window.open(url)
            }
        },
         // 获取栏目树
        fetchpaycontentAllTree(){
            this.$request.fetchpaycontentAllTree().then(res=>{
                if (res.code==200) {
                    let allobj = {
                                name: '全部',
                                id: ''
                            }
                    this.searchHeader.searchItems[2].options = res.data
                    this.searchHeader.searchItems[2].options.unshift(allobj)
                }
            })
        },
         // 级联数据
        fetchpaycontentSitesTree(){
            this.$request.fetchpaycontentSitesTree().then(res=>{
                if (res.code==200) {
                    this.searchHeader.searchItems[3].options = res.data
                }
            })
        },
        // 排序
        handleSortChange(column, prop, order){
            //  console.log(column,prop,order);
            let asc = 'ascending'
            let des = 'descending'
            if (prop=="amount"&&order==asc) {
                this.list.params.orderBy=1
            }else if (prop=="amount"&&order==des) {
                this.list.params.orderBy=2
            }else if (prop=="payTime"&&order==asc) {
                this.list.params.orderBy=4

            }else if (prop=="payTime"&&order==des) {
                this.list.params.orderBy=3
            }else{
                this.list.params.orderBy=3
            }
            this.fetchTableApi()
        },
        // 搜索
         handleFocus () {
            this.list.params.time2 = ''
            if (this.list.params.time === null) {
                this.list.params.time2 = ''
                this.list.params.time = []
                this.list.params.payStartTime = ''
                this.list.params.payEndTime = ''
            }
        },
        handleSearch(){
            // console.log(this.list.params);
            // this.list.params.numlength = this.list.params.ids.length
            if (this.list.params.ids&&this.list.params.ids.length<=1) {
                this.list.params.first = this.list.params.ids[0]
            }else if (this.list.params.ids&&this.list.params.ids.length>1) {
                this.list.params.first = this.list.params.ids[0]
                this.list.params.second = this.list.params.ids[1]
            }
            // this.list.params.userName = this.list.params.title
            if (this.list.params.time2 !== '') {
                this.list.params.time = []
            }
            if (this.list.params.sites&&this.list.params.sites.length>=1) {
                this.list.params.siteId =  this.list.params.sites[0]
            }
            if (this.list.params.time2) {
                if (this.list.params.time2 === 1) {
                this.list.params.payStartTime = this.getData(0)
                this.list.params.payEndTime = this.getData(0)
                } else if (this.list.params.time2 === 2) {
                this.list.params.payStartTime = this.getData(6)
                this.list.params.payEndTime = this.getData(0)
                } else if (this.list.params.time2 === 3) {
                this.list.params.payStartTime = this.getData(29)
                this.list.params.payEndTime = this.getData(0)
                } else if (this.list.params.time2 === 4) {
                this.list.params.payStartTime = ''
                this.list.params.payEndTime = ''
                }
                this.list.params.time[0] = this.list.params.payStartTime
                this.list.params.time[1] = this.list.params.payEndTime
            } else if (this.list.params.time === null) {
                this.list.params.time2 = ''
                this.list.params.time = []
                this.list.params.payStartTime = ''
                this.list.params.payEndTime = ''
            }
            if (this.list.params.time.length === 2) {
                this.list.params.payStartTime = this.list.params.time[0]
                this.list.params.payEndTime = this.list.params.time[1]
            }
            // this.list.params = qs.stringify(this.list.params)
            this.fetchTableApi()
            this.fetchpaycontentPaydetailOrderSum()
        },
        // 搜索
		handleEventBtnSearch(item) {
            let token = localStorage.getItem('JEECMS-Auth-Token')
            window.open(`${this.$path}${this.$prefix}/paydetails/order/export?${qs.stringify(this.list.params)}&JEECMS-Auth-Token=${token}`)
		},
        // 获取统计数据
        fetchpaycontentPaydetailOrderSum(){
            let data = Object.assign({}, this.list.params)
            delete data.time
            delete data.time2
            delete data.ids
            delete data.sites
            // console.log(this.list.params);
            this.$request.fetchpaycontentPaydetailOrderSum(data).then(res=>{
                if (res.code==200) {
                    this.orderData = res.data
                }
            })
        },
        // 获取表格数据
        fetchTableCallBack(res){
            if (res.code==200) {
                    this.formList.data = res.data.content
                    this.floatData()
                    this.formList.totalCount = res.data.totalElements
                }
        },
       floatData(){
            this.formList.data.forEach((item,index)=>{
                this.formList.data[index].payAmount = Number(item.payAmount)
                this.formList.data[index].readAmount = Number(item.readAmount)
                this.formList.data[index].rewardAmount = Number(item.rewardAmount)
            })
        },
         handleSizeChange (val) {
            this.list.params.size = val
            this.fetchTableApi()
        },
        handleCurrentChange (val) {
            this.list.params.page = val
            this.fetchTableApi()
        }

    },
    mounted(){
        this.fetchTableApi()
        this.fetchpaycontentPaydetailOrderSum()
        this.fetchpaycontentAllTree()
        this.fetchpaycontentSitesTree()
        this.handlePlatformOpen()
    },
    watch:{
        activeTab(num){
            // console.log(num);
            if (num==3) {
                this.fetchTableApi()
                this.fetchpaycontentPaydetailOrderSum()
            }
        }
    }
}
</script>
<style lang="scss">
    .order_main{
        .qiehuan{
            .el-radio-button__inner{
                 min-width: 74px;
            }
        }
        .bluep{
            width: 370px;
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
            width: 370px;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
            cursor: pointer;
            color: #ccc;
            span{
                color:#ccc;
            }
        }
        @media screen and (max-width: 1919px) {
            .z-width-md {
                width: 120px !important;
            }
        }
        .top{
            margin-bottom: 24px;
            margin-top: 12px;
            p{
                font-family: Microsoft YaHei;
                font-weight: 400;
                color: #666666;
                margin-right: 15px;
                font-size: 14px;
                .touone{
                    font-family: Microsoft YaHei;
                    font-weight: bold;
                    color: #333333;
                    font-size: 20px;
                }
                span{
                    font-size: 14px;
                    color: #666;
                }
            }
        }
        .shouyi{
            margin-bottom: 30px;
            display: flex;
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

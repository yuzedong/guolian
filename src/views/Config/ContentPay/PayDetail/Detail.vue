<template>
    <section class="detail_main">
        <div class="detailtop">
            <p v-if="topData.title.length<22" :class="topData.deleteFlag=='true'?'huise':'one'" @click="preivews">
                <span style="color:#666;">[{{topData.channelName}}]</span>
                {{topData.title}}
            </p>
            <p v-else class="z-title-md" :class="topData.deleteFlag?'huisep':'onep'" @click="preivews">
                <span style="color:#666;">[{{topData.channelName}}]</span>
                {{topData.title}}
            </p>
            <p>内容ID：<span class="secsp">{{topData.contentId}}</span></p>
            <p>创建人：<span class="secsp">{{topData.createUser}}</span></p>
            <p>发布时间：<span class="secsp">{{topData.releaseTime}}</span></p>
        </div>
         <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
             @handleSearch="handleSearch"
            @handleFocus='handleFocus'
		></search-header>
        <div class="top">
            <p>总金额(元)：<span>{{Number(centerData.payPriceSum)}}</span></p>
            <p class="shuxian">|</p>
           <p>打赏(元)：<span>{{Number(centerData.rewardSum)}}</span></p>
           <p class="shuxian">|</p>
           <p>付费阅读(元)：<span>{{Number(centerData.payreadSum)}}</span></p>
           <p class="shuxian">|</p>
           <p>微信支付(元)：<span>{{Number(centerData.wechatPaySum)}}</span></p>
           <p class="shuxian">|</p>
           <p>支付宝支付(元)：<span>{{Number(centerData.alipayPaySum)}}</span></p>
        </div>
        <div class="balance_form">
            <base-table v-bind="formList"
            @sortChange="handleSortChange"
            @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange"
            >
                <template #ipAddr="scope">
                    <p>{{scope.scope.row.username}}({{scope.scope.row.ipAddr}})</p>
                </template>
                <template #money="scope">
                    <p>{{Number(scope.scope.row.money)}}</p>
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
    name:'configPayDetailDetail',
    mixins: [searchHeader,baseTable],
    data(){
        return{
            
            topData:{
                title:'',
                channelName:'',
                createUser:'',
                releaseTime:'',
                deleteFlag:false,
                contentId:''
            },
            centerData:{
                payPriceSum:0,
                rewardSum:0,
                payreadSum:0,
                wechatPaySum:0,
                alipayPaySum:0
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
                                return time.getTime() > Date.now() - 8.64e6
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
                        width: '140px'
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
                        label: '付费类型',
                        value: 'type',
                        class: 'z-hidden-lg-and-down',
                        style: {
                        width: '140px'
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
                                label: '支付用户',
                                value: 1
                            },
                            {
                                label: '订单号',
                                value: 2
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
                        width:'200'
                    },
                    {
                        label: '金额(元)',
                        prop: 'money',
                        sortable: 'custom',
                        scopeType:'slot'
                    },
                    {
                        label: '支付方式(元)',
                        prop: 'payType',
                    },
                     {
                        label: '支付时间',
                        prop: 'payTime',
                        sortable: 'custom'
                    },
                     {
                        label: '支付用户及IP',
                        prop: 'ipAddr',
                        scopeType:'slot'
                    },
                     {
                        label: '订单号',
                        prop: 'code',
                        width:'200'
                    }
                ],
               
                showSelection: false,
                showPagination: true,
                showIndex: false,
                data: [],
                totalCount:0
            },
            list:{
                params:{
                    beginTime:'',
                    endTime:'',
                    contentId:'',
                    payType:'',
                    type:'',
                    keyType:1,
                    key:'',
                    sortType:1,
                    page:1,
                    size:10,
                    time2:4
                }
            }
        }
    },
    methods:{
         getTime,
        getData,
        getNewTime,
        // 内容预览
        preivews(){
            if (this.topData.deleteFlag=='true') {
                this.$confirm('内容已删除，无法预览', '提示', {
                    confirmButtonText: '关闭',
                    type: 'warning',
                    showCancelButton:false
                }).then((res) => {}).catch(() => {})
            }else{
                window.open(this.topData.url)
            }
        },
        // 排序
        handleSortChange(column, prop, order){
            let asc = 'ascending'
            let des = 'descending'
            if (prop=='money'&&order==asc) {
                this.list.params.sortType = 1
            }else if (prop=='money'&&order==des) {
                this.list.params.sortType = 2
            }else if (prop=='payTime'&&order==asc) {
                this.list.params.sortType = 3
            }else if (prop=='payTime'&&order==des) {
                this.list.params.sortType = 4
            }else{
                this.list.params.sortType = 1
            }
            this.fetchpaycontentPaydetailContentDetail()
        },
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
            } else if (this.list.params.time.length == 0) {
                this.list.params.time2 = 4
                this.list.params.time = []
                this.list.params.beginTime = ''
                this.list.params.endTime = ''
            }
            if (this.list.params.time.length === 2) {
                this.list.params.beginTime = this.list.params.time[0]
                this.list.params.endTime = this.list.params.time[1]
            }
            
            // if (this.list.params.time) {
            //     this.list.params.beginTime = this.list.params.time[0]
            //     this.list.params.endTime = this.list.params.time[1]
            // }
            this.fetchpaycontentPaydetailContentDetail()
            this.fetchpaycontentPaydetailContentDetailSurvey()
        },
        // 搜索
		handleEventBtnSearch(item) {
			let token = localStorage.getItem('JEECMS-Auth-Token')
            window.open(`${this.$path}${this.$prefix}/paydetails/exportContentDetailList?${qs.stringify(this.list.params)}&JEECMS-Auth-Token=${token}`)
		},
        // 获取概况数据
        fetchpaycontentPaydetailContentDetailSurvey(){
            this.$request.fetchpaycontentPaydetailContentDetailSurvey(this.list.params).then(res=>{
                if (res.code==200) {
                    this.centerData = res.data
                }
            })
        },
        // 获取表格数据
        fetchpaycontentPaydetailContentDetail(){
            this.list.params.contentId = this.$route.query.contentId
            delete this.list.params.time
            delete this.list.params.time2
            // this.list.params.contentId = '2572'
            this.$request.fetchpaycontentPaydetailContentDetail(this.list.params).then(res=>{
                if (res.code==200) {
                    this.formList.data = res.data.content
                    this.formList.totalCount = res.data.totalElements
                }
            })
        },
        handleSizeChange (val) {
            // console.log(val);
            this.list.params.size = val
            this.fetchpaycontentPaydetailContentDetail()
        },
        handleCurrentChange (val) {
            // console.log(val);
            this.list.params.page = val
            this.fetchpaycontentPaydetailContentDetail()
        }
    },
    mounted(){
        this.topData = this.$route.query
        this.fetchpaycontentPaydetailContentDetail()
        this.fetchpaycontentPaydetailContentDetailSurvey()
    },
    activated(){
        this.topData = this.$route.query
        this.fetchpaycontentPaydetailContentDetail()
        this.fetchpaycontentPaydetailContentDetailSurvey()
    }
}
</script>
<style lang="scss">
    .detail_main{
        @media screen and (max-width: 1919px) {
            .z-title-md {
                width: 320px !important;
            }
        }
        .detailtop{
            display: flex;
            // justify-content: space-around;
            margin-bottom: 30px;
            .one{
                color: #1ec6df;
                font-size: 16px;
                width: auto;
                // white-space:nowrap;
                // overflow:hidden;
                // text-overflow: ellipsis;
                cursor: pointer;
                margin-right: 60px;
            }
            .huise{
                color: #ccc ;
                font-size: 16px;
                width: auto;
                margin-right: 60px;
                // white-space:nowrap;
                // overflow:hidden;
                // text-overflow: ellipsis;
                cursor: pointer;
                span{
                    color: #ccc !important;
                }
            }
            .onep{
                color: #1ec6df;
                font-size: 16px;
                width: 415px;
                white-space:nowrap;
                overflow:hidden;
                text-overflow: ellipsis;
                cursor: pointer;
                margin-right: 60px;
            }
            .huisep{
                color: #ccc ;
                font-size: 16px;
                width: 415px;
                margin-right: 60px;
                white-space:nowrap;
                overflow:hidden;
                text-overflow: ellipsis;
                cursor: pointer;
                span{
                    color: #ccc !important;
                }
            }
            p{
                margin-right: 60px;
                color: #666;
                .secsp{
                    color: #333;
                }
            }
        }
        .top{
            display: flex;
            margin-bottom: 20px;
            margin-top: 20px;
            p{
                font-size: 14px;
                span{
                    font-size: 20px;
                }
            }
            .shuxian{
                font-size: 15px;
                color: #999;
                margin: 0px 15px;
                line-height: 23px;
            }
        }
    }
</style>
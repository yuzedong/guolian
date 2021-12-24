<template>
    <section class="content_main">

         <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
             @handleFocus='handleFocus'
             @handleSearch="handleSearch"
		></search-header>
        <div class="top">
            <p class="sum_money">总金额(元)：<span>{{Number(contentData.totalAmount)}}</span> &nbsp;&nbsp; <span style="font-size:14px;color:#666;">打赏(元)：{{Number(contentData.rewardAmount)}} &nbsp;&nbsp; 付费阅读(元)：{{Number(contentData.readAmount)}}</span></p>

        </div>
        <div class="balance_form">
            <base-table v-bind="formList"
            @handleDetail="handleDetail"
            @sortChange="handleSortChange"
             @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange"
            >
                <template #title="scope">
                    <el-tooltip v-if="scope.scope.row.title.length>14" :content="scope.scope.row.title" placement="bottom" effect="light">
                        <p class="z-width-hide" :class="scope.scope.row.deleteFlag?'huisep':'bluep'"  @click="showPreivew(scope.scope.row.deleteFlag,scope.scope.row.contentId,scope.scope.row.url)">[{{scope.scope.row.channelName}}] <span >{{scope.scope.row.title}}</span></p>
                    </el-tooltip>
                    <p v-else class="z-width-hide" :class="scope.scope.row.deleteFlag?'huisep':'bluep'"  @click="showPreivew(scope.scope.row.deleteFlag,scope.scope.row.contentId,scope.scope.row.url)">[{{scope.scope.row.channelName}}] <span >{{scope.scope.row.title}}</span></p>

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
    name:'Content',
    mixins: [searchHeader,baseTable],
    props:{
        activeTab:{
            type:String,
            default:''
        },
        channelId:{
            type:Number,
            default:0
        }
    },
    data(){
        return{
            contentData:{
                readAmount:0,
                rewardAmount:0,
                totalAmount:0
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
                        type: 'cascader',
                        value: 'sites',
                        // class: 'z-hidden-lg-and-down',
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
                        type: 'cascader',
                        value: 'alltree',
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
                        label: '选择栏目:',
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
                        label: '',
                        value: 'status',
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
                                value: 3
                            }
                        ]
                    },
                    {
                        type: 'searchInput',
                        value: 'selects',
                        hiddenKey: 'senior',
                        hiddenValue: true,
                        placeholder: '',
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
                        label: '内容标题',
                        prop: 'title',
                        scopeType:'slot',
                        maxWidth:"400",
                        minWidth:'120'
                    },
                    {
                        label: '内容ID',
                        prop: 'contentId',
                    },
                    {
                        label: '创建人',
                        prop: 'createUser',
                    },
                     {
                        label: '发布时间',
                        prop: 'releaseTime',
                        sortable: 'custom'
                    },
                     {
                        label: '付费总金额(元)',
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
                        prop: 'readAmount',
                        sortable: 'custom',
                        class:'aaaaa'
                    },
                     {
                        label: '最近支付时间',
                        prop: 'lastPayTime',
                        sortable: 'custom'
                    }
                ],
                handleColumnProp: {
                    label: '操作',
                    width: '80',
                },
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
                showSelection: false,
                showPagination: true,
                showIndex: false,
                totalCount:0,
                data: []
            },
            list:{
                api:'fetchpaycontentPaydetailContent',
                params:{
                    time:'',
                    time2:4,
                    beginTime:'',
                    endTime:"",
                    channelId:'',
                    title:'',
                    contentId:'',
                    createUser:'',
                    siteId:1,
                    orderBy:9,
                    page:1,
                    size:10,
                    selects:'',
                    status:1,
                     sites:[1],
                     alltree:[''],
                },
                filterParams:['time','time2','status','selects','sites','alltree']
            }

        }
    },
    methods:{
        getTime,
        getData,
        getNewTime,
        // 跳转预览页
        showPreivew(flag,id,url){
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
        // 获取栏目树
        fetchpaycontentAllTree(){
            this.$request.fetchpaycontentAllTree().then(res=>{
                if (res.code==200) {
                     res.data.forEach(item=>{
                        this.searchHeader.searchItems[3].options.push(item)
                    })
                    // console.log(res.data);
                    // this.searchHeader.searchItems[3].options = res.data
                }
            })
        },
         // 级联数据
        fetchpaycontentSitesTree(){
            this.$request.fetchpaycontentSitesTree().then(res=>{
                if (res.code==200) {
                    // console.log(res);
                    this.searchHeader.searchItems[2].options = res.data
                }
            })
        },
        // 排序
        handleSortChange(column, prop, order){
            let asc = 'ascending'
            let des = 'descending'
            if (prop=='releaseTime'&& order==asc) {
               this.list.params.orderBy = 2
            }else if (prop=='releaseTime'&& order==des) {
               this.list.params.orderBy = 1
            }else if (prop=='totalAmount'&& order==asc) {
               this.list.params.orderBy = 4
            }else if (prop=='totalAmount'&& order==des) {
               this.list.params.orderBy = 3
            }else if (prop=='rewardAmount'&& order==asc) {
               this.list.params.orderBy = 6
            }else if (prop=='rewardAmount'&& order==des) {
               this.list.params.orderBy = 5
            }else if (prop=='readAmount'&& order==asc) {
               this.list.params.orderBy = 8
            }else if (prop=='readAmount'&& order==des) {
               this.list.params.orderBy = 7
            }else if (prop=='lastPayTime'&& order==asc) {
               this.list.params.orderBy = 10
            }else if (prop=='lastPayTime'&& order==des) {
               this.list.params.orderBy = 9
            }else{
               this.list.params.orderBy = 9
            }
            this.fetchTableApi()
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
            // console.log(this.list.params);
            if (this.list.params.status==1) {
                this.list.params.title = this.list.params.selects
                this.list.params.contentId = ''
                this.list.params.createUser = ''
            }else if (this.list.params.status==2) {
                this.list.params.contentId = this.list.params.selects
                this.list.params.title = ''
                this.list.params.createUser = ''
            }else if (this.list.params.status==3) {
                this.list.params.createUser = this.list.params.selects
                this.list.params.title = ''
                this.list.params.contentId = ''
            }
            if (this.list.params.alltree&&this.list.params.alltree.length>=1) {
                this.list.params.channelId = this.list.params.alltree[this.list.params.alltree.length-1]
            }
            if (this.list.params.time2 !== '') {
                this.list.params.time = []
            }
           if (this.list.params.sites&&this.list.params.sites.length>=1) {
                this.list.params.siteId =  this.list.params.sites[0]
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
            }
            this.fetchTableApi()
            this.fetchpaycontentPaydetailContentSum()
        },
		handleEventBtnSearch(item) {
			let token = localStorage.getItem('JEECMS-Auth-Token')
            window.open(`${this.$path}${this.$prefix}/paydetails/content/export?${qs.stringify(this.list.params)}&JEECMS-Auth-Token=${token}`)
		},
        handleDetail(data){
            this.$routerLink('/config/content-pay/pay-detail/detail','list',{contentId:data.contentId,channelName:data.channelName,title:data.title,createUser:data.createUser,releaseTime:data.releaseTime,deleteFlag:data.deleteFlag,url:data.url})

        },
        // 获取汇总数据
        fetchpaycontentPaydetailContentSum(){
            let data = {
                beginTime: this.list.params.beginTime,
                endTime: this.list.params.endTime,
                channelId: this.list.params.channelId,
                title: this.list.params.title,
                contentId: this.list.params.contentId,
                createUser: this.list.params.createUser,
                siteId: this.list.params.siteId,
                orderBy: this.list.params.orderBy,
                page: this.list.params.page,
                size: this.list.params.size,
                selects: this.list.params.selects,
                status: this.list.params.status,
            }
            this.$request.fetchpaycontentPaydetailContentSum(data).then(res=>{
                if (res.code==200) {
                    this.contentData = res.data
                }
            })
        },
        //
        fetchTableCallBack(res){
            if (res.code==200) {
                this.formList.data = res.data.content
                this.floatData()
                this.formList.totalCount = res.data.totalElements
            }
        },
         floatData(){
            this.formList.data.forEach((item,index)=>{
                this.formList.data[index].rewardAmount = Number(item.rewardAmount)
                this.formList.data[index].totalAmount = Number(item.totalAmount)
                this.formList.data[index].readAmount = Number(item.readAmount)
            })
        },
        // fetchTableApi(){
        //     this.$request.fetchTableApi(this.list.params).then(res=>{

        //     })
        // },
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
       this.fetchpaycontentPaydetailContentSum()
       this.fetchpaycontentAllTree()
       this.fetchpaycontentSitesTree()
    },
    watch:{
        channelId(id){
            this.list.params.channelId = id
            this.list.params.alltree = [id]
            // this.fetchTableApi()
            // this.fetchpaycontentPaydetailContentSum()
        },
        activeTab(num){
            if (num==1) {
                this.fetchTableApi()
                this.fetchpaycontentPaydetailContentSum()
            }
        },


    }
}
</script>
<style lang="scss">
    .content_main{
        @media screen and (max-width: 1919px) {
            .z-width-hide {
                width: 150px !important;
            }
        }
        .bluep{
            width: 250px;
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
            width: 250px;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
            cursor: pointer;
            color: #ccc;
            span{
                color:#ccc;
            }
        }
        .top{
            display: flex;
            margin-bottom: 30px;
            margin-top: 11px;
            p{
                margin-right: 50px;
            }
            .sum_money{
                font-family: Microsoft YaHei;
                font-weight: 400;
                color: #666666;
                font-size: 14px;
                span{
                    font-family: Microsoft YaHei;
                    font-weight: 400;
                    color: #333333;
                    font-size: 20px;
                }
            }
        }
    }
</style>

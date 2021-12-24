<template>
    <section class="column_main">

         <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="list.params"
			@handleBtnSearch="handleEventBtnSearch"
            @handleFocus='handleFocus'
            @handleSearch="handleSearch"
		></search-header>
         <div class="top">
            <p class="sum_money">总金额(元)：<span>{{Number(columnData.payAmount)}}</span> &nbsp;&nbsp; <span style="font-size:14px;color:#999;">打赏(元)：{{Number(columnData.rewardAmount)}} &nbsp;&nbsp; 付费阅读(元)：{{Number(columnData.readAmount)}}</span></p>

        </div>
        <div class="balance_form">
            <base-table v-bind="formList"
             @handleDetail="handleTableDetail"
             @sortChange="handleSortChange"
             @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange"
             >
            </base-table>
        </div>
    </section>
</template>
<script>
import searchHeader from '@/components/mixins/searchHeader'
import baseTable from '@/components/mixins/baseTable'
import {  getTime, getData, getNewTime } from '@/utils'
import qs from 'qs'
import { orderBy } from 'lodash'

export default {
    name:'Column',
    mixins: [searchHeader,baseTable],
    props:{
        activeTab:{
            type:String,
            default:''
        }
    },
    data(){
        return{
            columnData:{
                rewardAmount:0,
                readAmount:0,
                sum:0
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
                        type: 'cascader',
                        value: 'manytype',
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
                        label: '统计方式:',
                        placeholder: '请选择',
                        options: [
                            {
                                name: '按顶层栏目',
                                id: 1
                            },
                            {
                                name: '按底层栏目',
                                id: 2
                            }
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
                    }
                ]
            },
            formList: {
                 columns: [
                    {
                        label: '栏目名称',
                        prop: 'channelName'
                    },
                    {
                        label: '付费总金额(元)',
                        prop: 'payAmount',
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
                        label: '付费阅读',
                        prop: 'readAmount',
                         align: 'left',
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
                    width: '80',
                },
                showSelection: false,
                showPagination: true,
                showIndex: false,
                data: [],
                totalCount:0
            },
           list:{
               api:'fetchpaycontentPaydetailColumn',
                params:{
                    time:'',
                    time2:4,
                    siteId:1,
                    type:1,
                    channelId:'',
                    orderBy:3,
                    beginTime:'',
                    endTime:'',
                    page:1,
                    size:10,
                    sites:[1],
                    manytype:[1]
                },
                filterParams:['time','time2','sites','manytype']

            }
        }
    },
    methods:{
         getTime,
        getData,
        getNewTime,
         // 获取栏目树
        fetchpaycontentAllTree(){
            this.$request.fetchpaycontentAllTree().then(res=>{
                if (res.code==200) {
                    res.data.forEach(item=>{
                        this.searchHeader.searchItems[3].options.push(item)
                    })
                    // console.log(this.searchHeader.searchItems[3].options);
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
            if (this.list.params.manytype&&this.list.params.manytype.length==1&&this.list.params.manytype[0]==1) {
                this.list.params.type = this.list.params.manytype[0]
                delete this.list.params.channelId
            }else if (this.list.params.manytype&&this.list.params.manytype.length==1&&this.list.params.manytype[0]==2) {
                this.list.params.type = this.list.params.manytype[0]
                delete this.list.params.channelId
            }else{
                this.list.type = ''
                this.list.params.channelId = this.list.params.manytype[this.list.params.manytype.length-1]
               delete this.list.params.type
            }
            if (this.list.params.sites&&this.list.params.sites.length>=1) {
                this.list.params.siteId =  this.list.params.sites[0]
            }
            // this.list.params.userName = this.list.params.title
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
            }
            this.fetchTableApi()
            this.fetchpaycontentPaydetailColumnSurvey()
        },
		handleEventBtnSearch(item) {
			let token = localStorage.getItem('JEECMS-Auth-Token')
            window.open(`${this.$path}${this.$prefix}/paydetails/exportChannel?${qs.stringify(this.list.params)}&JEECMS-Auth-Token=${token}`)
		},
        handleTableDetail(data){
            let obj={
                activeTab:"1",
                channelId:data.channelId
            }
            this.$emit('retrieve',obj)
        },
          // 排序
        handleSortChange(column, prop, order){
            // console.log(column,prop,order);
            let asc = 'ascending'
            let des = 'descending'
            if (prop=='payAmount'&&order==asc) {
                this.list.params.orderBy = 4
            }else if (prop=='payAmount'&&order==des) {
                this.list.params.orderBy = 3
            }else if (prop=='readAmount'&&order==asc) {
                this.list.params.orderBy = 8
            }else if (prop=='readAmount'&&order==des) {
                this.list.params.orderBy = 7
            }else if (prop=='rewardAmount'&&order==asc) {
                this.list.params.orderBy = 6
            }else if (prop=='rewardAmount'&&order==des) {
                this.list.params.orderBy = 5
            }else{
                this.list.params.orderBy = 3
            }
            this.fetchTableApi()
        },
        // 获取列表数据
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
        fetchpaycontentPaydetailColumnSurvey(){
            let data = {
                siteId: this.list.params.siteId,
                type: this.list.params.type,
                channelId: this.list.params.channelId,
                orderBy: this.list.params.orderBy,
                beginTime: this.list.params.beginTime,
                endTime: this.list.params.endTime,
                page: this.list.params.page,
                size: this.list.params.size,
            }
            this.$request.fetchpaycontentPaydetailColumnSurvey(data).then(res=>{
                if (res.code==200) {
                    this.columnData = res.data
                }
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
        this.fetchpaycontentPaydetailColumnSurvey()
        this.fetchpaycontentSitesTree()
        this.fetchpaycontentAllTree()
    },
    watch:{
        activeTab(num){
            if (num==2) {
                this.fetchpaycontentPaydetailColumnSurvey()
                this.fetchTableApi()
            }
        }
    }
}
</script>
<style lang="scss">
    .column_main{
        .qiehuan{
            .el-radio-button__inner{
                 min-width: 74px;
            }
        }
        .top{
            display: flex;
            margin-bottom: 30px;
            margin-top: 12px;
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
                    font-weight: bold;
                    color: #333333;
                    font-size: 20px;
                }
            }
        }
    }
</style>

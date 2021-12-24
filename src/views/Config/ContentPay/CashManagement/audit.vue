<template>
    <section class="audit_main">
        <!-- <div class="top">
            <p>总金额(元)：<span>5000</span> &nbsp;&nbsp; <span style="font-size:12px;color:#999;">[打赏(元)：2000 &nbsp;&nbsp; 付费阅读(元)：1000 ]</span></p>

        </div> -->
         <search-header
			class="search-header"
			v-bind="searchHeader"
			:params="params"
			 @handleSearch="handleSearchs"
		></search-header>
        <div class="balance_form">
            <base-table v-bind="formList"
            v-if="params.checkStatus==1"
             @handleDetail="handleTableDetail"
             @handleback="handleTableBack"
             @sortChange="handleSortChange"
             @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange">
                <template #accountFullNameStr="scope">
                    <div class="main_name">
                        <img v-if="scope.scope.row.headimgurl" :src="scope.scope.row.headimgurl" alt="" style="width:34px;height:34px;margin-right:10px;border-radius:50%;">
                        <el-tooltip :content="scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName" placement="bottom" effect="light">
                            <p class="sonp">{{scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName}}</p>
                        </el-tooltip>

                    </div>
                </template>
                <template #withdrawType="scope">
                    <p>{{scope.scope.row.withdrawType==1?'支付宝':scope.scope.row.withdrawType==2?'微信':''}}</p>
                </template>
                <template #createTime="scope">
                    <p class="z-width-md">{{scope.scope.row.createTime}}</p>
                </template>
                <template #balance="scope">
                    <p>{{Number(scope.scope.row.balance)}}</p>
                </template>
            </base-table>
             <base-table v-bind="passList"
             v-if="params.checkStatus==2"
             @sortChange="handleSortChange"
             @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange">
                <template #accountFullNameStr="scope">
                    <div class="main_name">
                        <img v-if="scope.scope.row.headimgurl" :src="scope.scope.row.headimgurl" alt="" style="width:34px;height:34px;margin-right:10px;border-radius:50%;">
                        <el-tooltip :content="scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName" placement="bottom" effect="light">
                            <p class="sonp">{{scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName}}</p>
                        </el-tooltip>

                    </div>
                </template>
                <template #withdrawType="scope">
                    <p>{{scope.scope.row.withdrawType==1?'支付宝':scope.scope.row.withdrawType==2?'微信':''}}</p>
                </template>
                <template #createTime="scope">
                    <p class="z-width-md">{{scope.scope.row.createTime}}</p>
                </template>
                <template #reviewTime="scope">
                    <p class="z-width-md">{{scope.scope.row.reviewTime}}</p>
                </template>
            </base-table>
            <base-table v-bind="backList"
             v-if="params.checkStatus==3"
             @sortChange="handleSortChange"
             @handleSizeChange="handleSizeChange"
            @handleCurrentChange="handleCurrentChange">
               <template #remark="scope">
                   <el-tooltip v-if="scope.scope.row.remark.length>13" :content="scope.scope.row.remark" placement="bottom" effect="light">
                        <p class="remarkp z-width-md">{{scope.scope.row.remark}}</p>
                    </el-tooltip>
                   <p v-else class="remarkp z-width-md" >{{scope.scope.row.remark}}</p>
               </template>
               <template #accountFullNameStr="scope">
                    <div class="main_name">
                        <img v-if="scope.scope.row.headimgurl" :src="scope.scope.row.headimgurl" alt="" style="width:34px;height:34px;margin-right:10px;border-radius:50%;">
                        <el-tooltip :content="scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName" placement="bottom" effect="light">
                            <p class="sonp">{{scope.scope.row.withdrawType==1?scope.scope.row.account+'/'+scope.scope.row.fullName:scope.scope.row.fullName}}</p>
                        </el-tooltip>


                    </div>
                </template>
               <template #withdrawType="scope">
                    <p>{{scope.scope.row.withdrawType==1?'支付宝':scope.scope.row.withdrawType==2?'微信':''}}</p>
                </template>
                <template #createTime="scope">
                    <p class="z-width-md">{{scope.scope.row.createTime}}</p>
                </template>
                <template #rejectedTime="scope">
                    <p class="z-width-md">{{scope.scope.row.rejectedTime}}</p>
                </template>
            </base-table>
        </div>
        <form-dialog
         title="驳回"
         ref="backDialog"
         :form="backForm"
        :rules="backRules"
        :formItems="backFormItems"
        :handleClose="handleClose"
        :buttons="backButtons"
        @handleConfirm="handleConfirmback"
        >

        </form-dialog>
    </section>
</template>
<script>
import searchHeader from '@/components/mixins/searchHeader'
import baseTable from '@/components/mixins/baseTable'
import formDialog from '@/components/mixins/formDialog'
import FormDialog from '../../../../components/dialog/FormDialog.vue'

export default {
  components: { FormDialog },
    name:'Audit',
    mixins: [searchHeader,baseTable,formDialog],
    props:{
        activeTab:{
            type:String,
            default:''
        }
    },
    data(){
        return{
            flag:false,
            backRules: {
                remark: [
                    this.$rules.required()
                ]
            },
            backForm: {
                remark: '',
                id:''
            },
            backFormItems: [
                {
                prop: 'remark',
                label: '驳回原因',
                type: 'textarea',
                maxlength: 500,
                autosize: { minRows: 5, maxRows: 10 },
                placeholder: '请输入驳回原因',
                showWordLimit:'show-word-limit'
                },
            ],
            backButtons: [
                {
                text: '确定',
                type: 'Submit'
                }
            ],

            searchHeader: {
                searchItems: [
                    {
                        type: 'cutButton',
                        value: 'checkStatus',
                        label: '',
                        options: [
                            {
                                label: '待审核',
                                value: 1
                            },
                            {
                                label: '审核通过',
                                value: 2
                            },
                            {
                                label: '驳回',
                                value: 3
                            }
                        ]
                    },
                    {
                        type: 'searchInput',
                        value: 'userName',
                        placeholder: '请输入用户名',
                        style: {
                        width: '335px'
                        }
                    },

                ]
            },
            formList: {
                 columns: [
                    {
                        label: '申请人用户名',
                        prop: 'userName',
                        maxWidth:'200'
                    },
                    {
                        label: '提现金额(元)',
                        prop: 'amount',
                         align: 'left',
                        sortable: 'custom',
                        maxWidth:'200'
                    },
                    {
                        label: '账户余额(元)',
                        prop: 'balance',
                        align: 'left',
                        sortable: 'custom',
                        maxWidth:'200',
                        scopeType:'slot'
                    },
                     {
                        label: '提现方式',
                        prop: 'withdrawType',
                        align: 'left',
                        scopeType:'slot',
                        maxWidth:'200'
                    },
                    {
                        label: '提现账号',
                        prop: 'accountFullNameStr',
                        align: 'left',
                        scopeType:'slot',
                        width:'380'
                    },
                     {
                        label: '申请时间',
                        prop: 'createTime',
                        align: 'left',
                        sortable: 'custom',
                        scopeType:'slot',
                        width:'180'
                    }
                ],
                handleColumn:[
                    {
                        type: 'Detail',
                        name: '审核通过',
                        icon: 'shenghetongguo',
                        // disabled: !this._checkPermission('/questionnaire/release', 'PUT'),
                        iconStyle: {
                        fontSize: '14px'
                        }
                    },
                    {
                       type: 'back',
                        name: '驳回',
                        icon: 'bohui',
                        // disabled: !this._checkPermission('/questionnaire/release', 'PUT'),
                        iconStyle: {
                        fontSize: '14px'
                        }
                    }
                ],
                handleColumnProp: {
                    label: '操作',
                    width: '180',
                },
                showSelection: false,
                showPagination: true,
                showIndex: false,
                data: [],
                totalCount:0
            },
            passList:{
                  columns: [
                    {
                        label: '申请人用户名',
                        prop: 'userName',
                        width:'180'
                    },
                    {
                        label: '提现金额(元)',
                        prop: 'amount',
                         align: 'left',
                        sortable: 'custom',
                        width:'180'
                    },
                     {
                        label: '提现方式',
                        prop: 'withdrawType',
                        align: 'left',
                        scopeType:'slot',
                        width:'180'
                    },
                    {
                        label: '提现账号',
                        prop: 'accountFullNameStr',
                        align: 'left',
                        scopeType:'slot',
                        minWith:'300',
                        maxWidth:'400'
                    },
                     {
                        label: '申请时间',
                        prop: 'createTime',
                        align: 'left',
                        sortable: 'custom',
                        scopeType:'slot'
                    },
                    {
                        label: '审核时间',
                        prop: 'reviewTime',
                        align: 'left',
                        sortable: 'custom',
                        scopeType:'slot'
                    },
                    {
                        label: '操作人用户名',
                        prop: 'updateUser',
                        align: 'left',
                        width:'120'
                    }
                ],
                showSelection: false,
                showPagination: true,
                showIndex: false,
                data: [],
                totalCount:0
            },
            backList:{
                  columns: [
                    {
                        label: '申请人用户名',
                        prop: 'userName',
                        width:'140'
                    },
                    {
                        label: '提现金额(元)',
                        prop: 'amount',
                         align: 'left',
                        sortable: 'custom',
                        width:'140'
                    },
                     {
                        label: '提现方式',
                        prop: 'withdrawType',
                        align: 'left',
                        scopeType:'slot',
                        width:'120'
                    },
                    {
                        label: '提现账号',
                        prop: 'accountFullNameStr',
                        align: 'left',
                        scopeType:'slot',
                        maxWidth:'360',
                        minWith:'240'
                    },
                     {
                        label: '申请时间',
                        prop: 'createTime',
                        align: 'left',
                        sortable: 'custom',
                        scopeType:'slot'
                    },
                    {
                        label: '驳回时间',
                        prop: 'rejectedTime',
                        align: 'left',
                        sortable: 'custom',
                        scopeType:'slot'
                    },
                    {
                        label: '驳回原因',
                        prop: 'remark',
                        align: 'left',
                        scopeType:'slot'
                    },
                    {
                        label: '操作人用户名',
                        prop: 'updateUser',
                        align: 'left',
                        width:'120'
                    }
                ],
                showSelection: false,
                showPagination: true,
                showIndex: false,
                data: [],
                totalCount:0
            },
            params:{
                userName:'',
                startDate:'',
                endDate:'',
                withdrawType:'',
                orderType:4,
                userId:'',
                nameOrAccountOrTransactionSerial:'',
                checkStatus:1,
                page:1,
                size:10
            }
        }
    },
    methods:{
           // 排序
        handleSortChange(column, prop, order){
            this.flag = true
            let asc = 'ascending'
            let des = 'descending'
            if (prop=='amount'&&order==asc) {
                this.params.orderType = 1
            }else if (prop=='amount'&&order==des) {
                this.params.orderType = 2
            }else if (prop=='balance'&&order==asc) {
                this.params.orderType = 7
            }else if (prop=='balance'&&order==des) {
                this.params.orderType = 8
            }else if (prop=='createTime'&&order==asc) {
                this.params.orderType = 3
            }else if (prop=='createTime'&&order==des) {
                this.params.orderType = 4
            }else if (prop=='reviewTime'&&order==asc) {
                this.params.orderType = 5
            }else if (prop=='reviewTime'&&order==des) {
                this.params.orderType = 6
            }else if (prop=='rejectedTime'&&order==asc) {
                this.params.orderType = 9
            }else if (prop=='rejectedTime'&&order==des) {
                this.params.orderType = 10
            }else{
                this.params.orderType = 4
            }
            this.fetchpaycontentCashManagementAudit()
        },
        // 搜索
		handleSearchs(item) {
            this.fetchpaycontentCashManagementAudit()
		},
        handleTableDetail(val){
            // console.log(val);
            this.$request.fetchpaycontentCashmanagementAuditPass(val.id).then(res=>{
                if (res.code==200) {
                    this.$message.success('审核通过')
                    this.fetchpaycontentCashManagementAudit()
                }
            })
        },
        // 打开驳回弹窗
        handleTableBack(val){
            this.backForm.id = val.id
            this.$refs.backDialog.showDialog()
        },
        // 关闭驳回弹窗
         handleClose (done){
             done()
         },
        //  提交驳回原因
         handleConfirmback(data){
             this.$request.fetchpaycontentCashmanagementRejected(data).then(res=>{
                 if (res.code==200) {
                     this.$message.success('操作成功')
                    this.fetchpaycontentCashManagementAudit()
                 }
             })
         },
        //  获取表格数据
         fetchpaycontentCashManagementAudit(){
            //  if (this.params.checkStatus==3){
            //      if(this.flag == false){
            //         this.params.orderType = 10
            //      }
            //  }else{
            //      if(this.flag == false){
            //         this.params.orderType = 4
            //      }
            //  }
            //  this.flag = false
             this.$request.fetchpaycontentCashManagementAudit(this.params).then(res=>{
                 if (res.code==200) {
                     if (this.params.checkStatus==1) {
                         this.formList.data = res.data.content
                         this.formList.totalCount = res.data.totalElements
                     }else if (this.params.checkStatus==2) {
                         this.passList.data = res.data.content
                         this.passList.totalCount = res.data.totalElements
                     }else if (this.params.checkStatus==3) {

                         this.backList.data = res.data.content
                         this.backList.totalCount = res.data.totalElements
                     }
                 }
             })
         },
        handleSizeChange (val) {
            this.params.size = val
            this.fetchpaycontentCashManagementAudit()
        },
        handleCurrentChange (val) {
            this.params.page = val
            this.fetchpaycontentCashManagementAudit()
        }
    },
    mounted(){
        this.fetchpaycontentCashManagementAudit()

    },
    watch:{
        activeTab(num){
            if (num==1) {
                this.fetchpaycontentCashManagementAudit()
            }
        }
    }
}
</script>
<style lang="scss">
    .audit_main{
        @media screen and (max-width: 1919px) {
            .z-width-md {
                width: 80px !important;
            }
        }
        .balance_form{
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
            .remarkp{
                white-space:nowrap;
                overflow:hidden;
                text-overflow: ellipsis;
                width: 200px;
                cursor: pointer;
            }
        }
        .top{
            display: flex;
            margin-bottom: 20px;
            p{
                margin-right: 50px;
            }
        }
        .el-dialog{
            width:450px !important;
            .el-dialog__body{
                overflow-y: hidden !important;
            }
            .el-form-item__label{
                width: 95px !important;
                text-align: left;
                padding-right: 10px;
            }
            .el-form-item__content{
                margin-left: 95px !important;
                .el-input__count{
                    color: #666;
                    bottom: -24px !important;
                    right: 0px;
                }
            }
            .el-form-item{
                margin-bottom: 25px !important;
            }
        }
    }
</style>

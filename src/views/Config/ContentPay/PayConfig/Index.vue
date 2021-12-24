<template>
  <section class="config-pay-config-index">
    <base-form
      v-bind="form"
      v-on:handleConfirm="handleConfirm"
      v-loading="loading"
    >
    <template #money="scope">
      <div>
        <!-- scope -->
        <el-tag
          :key="tag"
          v-for="tag in tags"
          closable
          :disable-transitions="false"
          class="tag-item"
          @close="handleClose(tag)">
          {{tag}}
        </el-tag>
        <el-input
          class="input-new-tag"
          v-if="inputVisible"
          v-model="inputValue"
          ref="saveTagInput"
          size="mini"
          @keyup.enter.native="handleInputConfirm"
          @blur="handleInputConfirm"
        >
        </el-input>
        <el-button v-else class="button-new-tag" size="small" @click="showInput">+ </el-button>
      </div>
    </template>
    <template #moneyMin="scope">
      <div class="integral">
        <el-input v-model="scope.form.moneyMin" maxlength="11" placeholder="请输入最小值" style="width:195px;margin-right:10px;">
        </el-input>-<el-input v-model="scope.form.moneyMax" style="width:195px;margin-left:10px;" maxlength="11" placeholder="请输入最大值"  @keyup.native="number"></el-input>
           <jee-popover  popoverText='可输入的最小金额为1，最大金额为10000，只能输入正整数' />

      </div>
    </template>
    <template #withdrawMin="scope">
      <div class="integral">
        <el-input v-model="scope.form.withdrawMin" maxlength="11" placeholder="请输入最小值" style="width:195px;margin-right:10px;">
        </el-input>-<el-input v-model="scope.form.withdrawMax" style="width:195px;margin-left:10px;" maxlength="11" placeholder="请输入最大值"  @keyup.native="number"></el-input>
           <jee-popover  popoverText='可输入的最小金额为1，最大金额为10000，只能输入正整数；' />

      </div>
    </template>
    <template #lianjie="scope">
      <p style="color:#999;">前往<span style="color:#1EC6DF;cursor: pointer;" @click="toOpen">微信商户平台</span></p>
    </template>
    </base-form>
  </section>
</template>
<script>
import baseForm from '@/components/mixins/baseForm'
import baseHeader from '@/components/mixins/baseHeader'
import JeePopover from '../../../../components/common/JeePopover.vue'

export default {
  components: { JeePopover },
  name: 'configPayConfigIndex',
  mixins: [baseForm, baseHeader],
  data () {
    var photomax = (rule, value, callback) => {
      var reg = /^(0|\+?[1-9][0-9]*)$/
      if (!reg.test(value)) {
        callback(new Error('只能输入0或正整数'))
      } else {
        callback()
      }
    }
    var moneyreg = (rule, value, callback) => {
    //   console.log(this.form.form.moneyMax);
      var reg1 = /^([1-9]\d{0,3}|10000)$/
      if (!reg1.test(value)) {
        callback(new Error('只能输入1-10000的正整数'))
      } else if (!reg1.test(this.form.form.moneyMax)) {
        callback(new Error('只能输入1-10000的正整数'))
      } else if (Number(this.form.form.moneyMax) <= Number(value)) {
        callback(new Error('最大值不能小于等于最小值'))
      } else {
        callback()
      }
    }
    var withdrawreg = (rule, value, callback) => {
      var reg2 = /^([1-9]\d{0,3}|10000)$/
      if (!reg2.test(value)) {
        callback(new Error('只能输入1-10000的正整数'))
      } else if (!reg2.test(this.form.form.withdrawMax)) {
        callback(new Error('只能输入1-10000的正整数'))
      } else if (Number(this.form.form.withdrawMax) <= Number(value)) {
        callback(new Error('最大值不能小于等于最小值'))
      } else {
        callback()
      }
    }
    var integerReg = (rule, value, callback) => {
      var reg3 = /^[1-9]\d*$/
      if (!value) {
        callback()
      } else if (!reg3.test(value)) {
        callback(new Error('只能输入正整数'))
      } else {
        callback()
      }
    }
    var commissionReg = (rule, value, callback) => {
      var reg4 = /^([1-9][0-9]{0,1}|100)$/
      if (!reg4.test(value)) {
        callback(new Error('可输入1~100间的数值'))
      } else {
        callback()
      }
    }
    var readReg = (rule, value, callback) => {
      var reg5 = /^\d{1,4}$|^10000$/
      if (!reg5.test(value)) {
        callback(new Error('可输入0~99999间的数值'))
      } else {
        callback()
      }
    }
    var priceReg = (rule, value, callback) => {
      var reg6 = /^[0-9]+(.[0-9]{1,2})?$/
      if (value > 10000 || value < 0.01) {
        callback(new Error('可输入0.01~10000间的数值'))
      } else if (!reg6.test(value)) {
        callback(new Error('可输入0.01~10000间的数值'))
      } else {
        callback()
      }
    }
    return {
      loading: false,
      tags: [1],
      inputValue: '',
      inputVisible: false,
      form: {
        labelWidth: '195px',
        api: '',
        form: {
          wechatOpen: 0,
          wechatAppid: '',
          wechatAccount: '',
          wechatSecret: '',
          alipayOpen: 0,
          alipayAppid: '',
          alipaySecret: '',
          alipayPublic: '',
          payPrice: '',
          payRead: '',
          payPeople: 0,
          payPhotoMax: '',
          money: [],
          moneyOpen: 0,
          moneyLogin: 0,
          moneyPeople: '',
          moneyPhotoMax: '',
          platform: 0,
          platformScale: '',
          platformDay: '',
          withdraw: 0,
          withdrawType: 3,
          withdrawAudit: 0,
          withdrawMin: '',
          withdrawMax: '',
          moneyMax: '',
          moneyMin: ''
        },
        formItems: [
          {
            type: 'title',
            label: '收款账号配置',
            titleClass: 'configMargin'
          },
          {
            type: 'radio',
            prop: 'wechatOpen',
            label: '微信收款及转账：',
            options: [{
              label: '开启',
              value: 1
            },
            {
              label: '关闭',
              value: 0
            }]
          },
          {
            prop: 'wechatAppid',
            label: '微信公众号AppId：',
            hiddenKey: 'wechatOpen',
            hiddenValue: 1,
            maxlength: '120',
            popoverText: '可请输入微信商户号所关联的公众号的AppId，若未关联，请前往微信支付-商户平台中进行设置',
            placeholder: '请输入内容'
          },
          {
            prop: 'wechatAccount',
            label: '微信商户号：',
            hiddenKey: 'wechatOpen',
            hiddenValue: 1,
            placeholder: '请输入内容',
            maxlength: '120'
          },
          {
            type: 'slot',
            prop: 'lianjie',
            label: '',
            hiddenKey: 'wechatOpen',
            hiddenValue: 1,
            itemClass: 'shanghuhao'
          },
          {
            prop: 'wechatSecret',
            label: '微信商户密钥：',
            type: 'textarea',
            hiddenKey: 'wechatOpen',
            hiddenValue: 1,
            maxlength: '1000',
            placeholder: '请输入内容',
            autosize: { minRows: 5 }
          },
          {
            type: 'radio',
            prop: 'alipayOpen',
            label: '支付宝收款及转账：',
            options: [{
              label: '开启',
              value: 1
            },
            {
              label: '关闭',
              value: 0
            }]
          },
          {
            prop: 'alipayAppid',
            label: '支付宝AppId：',
            hiddenKey: 'alipayOpen',
            placeholder: '请输入内容',
            hiddenValue: 1,
            maxlength: '120'
          },
          {
            prop: 'alipaySecret',
            label: '支付宝私钥：',
            hiddenKey: 'alipayOpen',
            placeholder: '请输入内容',
            hiddenValue: 1,
            maxlength: '2500'
          },
          {
            prop: 'alipayPublic',
            label: '支付宝公钥：',
            type: 'textarea',
            maxlength: 2500,
            placeholder: '请输入内容',
            hiddenKey: 'alipayOpen',
            hiddenValue: 1,
            autosize: { minRows: 5 }
          },
          {
            type: 'title',
            label: '付费阅读配置'
          },
          {
            prop: 'payPrice',
            placeholder: '请输入内容',
            label: '默认售价：',
            popoverText: '设置后仅作为初始数据显示在“付费阅读”模型中；可输入0.01~10000之间的数字，只能输入小数点后两位；'
          },
          {
            prop: 'payRead',
            placeholder: '请输入内容',
            label: '默认试读字数：',
            popoverText: '设置后仅作为初始数据显示在“付费阅读”模型中；可输入0~99999之间的整数，设为0表示不允许试读；试读针对的是富文本编辑器中的纯文字内容；'
          },
          {
            type: 'radio',
            prop: 'payPeople',
            label: '显示付费人数：',
            options: [{
              label: '开启',
              value: 1
            },
            {
              label: '关闭',
              value: 0
            }],
            popoverText: '开启后，在付费按钮下方会显示实际已付费人数'
          },
          {
            prop: 'payPhotoMax',
            label: '最大显示头像数量：',
            hiddenKey: 'payPeople',
            placeholder: '请输入内容',
            hiddenValue: 1,
            popoverText: '只能输入0或正整数，输入0则不显示头像'
          },
          {
            type: 'title',
            label: '打赏配置'
          },
          {
            prop: 'money',
            type: 'slot',
            label: '设置固定打赏金额(元)：',
            itemClass: 'moneymany'
          },
          {
            type: 'radio',
            prop: 'moneyOpen',
            label: '自定义打赏金额：',
            options: [{
              label: '开启',
              value: 1
            },
            {
              label: '关闭',
              value: 0
            }]
          },
          {
            type: 'slot',
            prop: 'moneyMin',
            label: '自定义金额范围(元)：',
            hiddenKey: 'moneyOpen',
            hiddenValue: 1,
            popoverText: '可输入的最小金额为1，最大金额为10000，只能输入正整数'
          },
          {
            type: 'radio',
            prop: 'moneyLogin',
            label: '打赏需要登录：',
            options: [{
              label: '开启',
              value: 1
            },
            {
              label: '关闭',
              value: 0
            }]
          },
          {
            type: 'radio',
            prop: 'moneyPeople',
            label: '显示打赏人数：',
            options: [{
              label: '开启',
              value: 1
            },
            {
              label: '关闭',
              value: 0
            }],
            popoverText: '开启后，在打赏按钮下方会显示实际已打赏人数'
          },
          {
            prop: 'moneyPhotoMax',
            label: '最大显示头像数量：',
            hiddenKey: 'moneyPeople',
            placeholder: '请输入内容',
            hiddenValue: 1,
            popoverText: '只能输入0或正整数，输入0则不显示头像'
          },
          {
            type: 'title',
            label: '平台抽成配置'
          },
          {
            type: 'radio',
            prop: 'platform',
            label: '平台抽成：',
            options: [{
              label: '开启',
              value: 1
            },
            {
              label: '关闭',
              value: 0
            }],
            popoverText: '开启后将从付费金额中扣除相应的比例，剩余部分归内容发布者所有；'
          },
          {
            prop: 'platformScale',
            label: '抽成比例(百分比)：',
            placeholder: '请输入内容',
            hiddenKey: 'platform',
            hiddenValue: 1,
            popoverText: '可输入1~100间的正整数'
          },
          {
            prop: 'platformDay',
            label: '结算周期(天)：',
            placeholder: '请输入',
            hiddenKey: 'platform',
            hiddenValue: 1,
            popoverText: '留空实时结算，只能输入正整数'
          },
          {
            type: 'title',
            label: '提现配置'
          },
          {
            type: 'radio',
            prop: 'withdraw',
            label: '余额提现：',
            options: [{
              label: '开启',
              value: 1
            },
            {
              label: '关闭',
              value: 0
            }]
          },
          {
            type: 'slot',
            prop: 'withdrawMin',
            label: '单次提现金额限制(元)：',
            hiddenKey: 'withdraw',
            hiddenValue: 1,
            popoverText: '可输入的最小金额为1，最大金额为10000，只能输入正整数；'
          },
          {
            type: 'radio',
            prop: 'withdrawType',
            label: '提现方式：',
            hiddenKey: 'withdraw',
            hiddenValue: 1,
            options: [{
              label: '微信提现',
              value: 2
            },
            {
              label: '支付宝提现',
              value: 1
            },
            {
              label: '全选',
              value: 3
            }]
          },
          {
            type: 'radio',
            prop: 'withdrawAudit',
            label: '提现审核：',
            hiddenKey: 'withdraw',
            hiddenValue: 1,
            options: [{
              label: '开启',
              value: 1
            },
            {
              label: '关闭',
              value: 0
            }]
          }
        ],
        rules: {
          payPrice: [
            { validator: priceReg, trigger: ['change', 'blur'] }
          ],
          payRead: [
            { validator: readReg, trigger: ['change', 'blur'] }
          ],
          wechatAppid: [
            this.$rules.required()
            // { validator: nameUnique, trigger: ['change', 'blur'] }
          ],
          wechatAccount: [
            this.$rules.required()
          ],
          wechatSecret: [
            this.$rules.required()
          ],
          alipayAppid: [
            this.$rules.required()
          ],
          alipaySecret: [
            this.$rules.required()
          ],
          alipayPublic: [
            this.$rules.required()
          ],
          money: [
            this.$rules.required()
          ],
          moneyMin: [
            this.$rules.required(),
            { validator: moneyreg, trigger: ['change', 'blur'] }
          ],
          moneyPhotoMax: [
            this.$rules.required(),
            { validator: photomax, trigger: ['change', 'blur'] }
          ],
          payPhotoMax: [
            this.$rules.required(),
            { validator: photomax, trigger: ['change', 'blur'] }
          ],
          platformScale: [
            this.$rules.required(),
            { validator: commissionReg, trigger: ['change', 'blur'] }
          ],
          platformDay: [
            { validator: integerReg, trigger: ['change'] }
          ],
          withdrawMin: [
            this.$rules.required(),
            { validator: withdrawreg, trigger: ['change', 'blur'] }
          ],
          withdrawType: [
            this.$rules.required()
          ]
        },
        submitBtns: [{
          text: '保存',
          subType: 'submit'
          // disabled: !this._checkPermission('/hotWordCategorys', 'PUT')
        }]
      }
    }
  },
  methods: {
    // 跳转到微信商户
    toOpen () {
      window.open('https://pay.weixin.qq.com/index.php/core/home/login?return_url=%2F')
    },
    // 获取表单详情
    handleDetail () {
      this.$request.fetchPayConfig().then(res => {
        if (res.code == 200) {
          // console.log(res.data);
          this.form.form = res.data
          this.tags = res.data.money
        }
      })
    },
    handleConfirm (data) {
      data.money = this.tags
      // this.loading = true
      this.$request.fetchPayConfigSave(data).then(res => {
        if (res.code == 200) {
          this.$message.success('保存成功')
          this.handleDetail()
        }
        setTimeout(() => {
          this.loading = false
        }, 2000)
      })
    },
    handleClose (tag) {
      this.tags.splice(this.tags.indexOf(tag), 1)
      this.form.form.money = this.tags
    },

    showInput () {
      this.inputVisible = true
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus()
      })
    },

    handleInputConfirm () {
      let inputValue = this.inputValue
      let reg = /^\d{1,4}$|^10000$/
      //   console.log(inputValue);
      //   console.log(!reg.test(inputValue));
      if (inputValue > 0 && reg.test(inputValue)) {
        this.tags.push(Number(inputValue))
        this.form.form.money = this.tags
      } else if (inputValue) {
        this.$message.error('只能输入1-10000的整数')
        return
      } else {
        return
      }
      this.inputVisible = false
      this.inputValue = ''
    },
    number (val) {

    }
  },
  mounted () {
    this.handleDetail()
  }
}
</script>

<style lang="scss">
.config-pay-config-index{
  .configMargin{
    display: flex;
    align-items: center;
    margin-bottom: 30px;
    .jee-bg-light{
      width: 2px;
      height: 14px;
    }
    h1{
      padding-left: 6px;
    }
  }
  .shanghuhao{
    margin-top: -30px;
    margin-bottom:15px !important;
  }
  .moneymany{
    .el-form-item__content{
      width: 425px;
    }
  }
  .tag-item{
    margin-right: 10px;
    margin-top: 10px;
    cursor: pointer;
    width: 96px;
    height: 40px;
    background: #FFFFFF;
    border: 1px solid #E8E8E8;
    border-radius: 2px;
    line-height: 40px;
    color: #333;
    position: relative;
    padding-left: 20px;
    font-size: 14px;
    .el-tag__close{
      display: none;
      color: #fff;
      background: #ccc;
      width: 14px;
      height: 14px;
      position: absolute;
      top: 12px;
      left: 60px;
      right: 0;
    }
    &:hover .el-tag__close{
      display: inline-block;
    }
    &:hover{
      border: 1px solid #1EC6DF;
      .el-tag__close{
        // float: right;
        right: -40px;
      }
    }
  }
  // .tag-item:nth-child(4n){
  //   display: block;
  // }
  .button-new-tag{
    width: 96px;
    height: 40px;
    background: #FFFFFF;
    border: 1px solid #E8E8E8;
    border-radius: 2px;
    line-height: 40px;
    padding: 0px !important;
    color: #333;
    margin-top: 10px;
    vertical-align: bottom;
    span{
      font-size: 32px;
      color: #999;
    }
  }
  .button-new-tag:hover span{
    color: #fff;
  }
  .input-new-tag{
    width: 96px;
    height: 40px;
    // margin-left: 10px;
    font-size: 14px;
    .el-input__inner{
      height: 40px;
      margin-top: 10px;
    }
    // vertical-align: bottom;
  }
}
</style>

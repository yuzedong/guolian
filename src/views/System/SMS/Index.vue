<template>
  <section>
    <!-- <iframe :src="url" width="100%" height="100%" allow="microphone; camera"> -->
        <!-- IE：你们都看我干吗，我现在也是支持的 -->
    <!-- </iframe> -->
    <section class="sms">
      <base-header v-bind="headers"
      v-on:handleCreate="handleHeaderCreate"
      />
      <base-table
        v-bind="list"
        v-on:handleSelectionChange="handleSelectionChange"
        v-on:handleSizeChange="handleSizeChange"
        v-on:handleCurrentChange="handleCurrentChange"
        v-on:handleRedact="handleTableRedact"
      >
      </base-table>
      <form-dialog
        ref="editSmsDialog"
        title="编辑短信模板"
        :buttons='dialogButtons'
        :loading="addFormLoading"
        :form="addForm"
        :formItems="addFormItems"
        v-on:handleConfirm="handleConfirmAdd"
      ></form-dialog>
    </section>
  </section>
</template>

<script>
import baseHeader from '@/components/mixins/baseHeader'
import baseTable from '@/components/mixins/baseTable'
import formDialog from '@/components/mixins/formDialog'

const columns = [
  {
    prop: 'mesTitle',
    label: '模板名称',
    formatter: (row, column, cellValue, index) => {
      const detailsMap = row.detailsMap || {}
      const phone = detailsMap.phone || {}
      return phone.mesTitle || ''
    }
  },
  {
    prop: 'mesContent',
    label: '模板内容',
    minWidth: '120px',
    formatter: (row, column, cellValue, index) => {
      const detailsMap = row.detailsMap || {}
      const phone = detailsMap.phone || {}
      return phone.mesContent || ''
    }
  }
]
const smsTemplate = '您好，随机码为{randomCode}，有效期为{validMinutes}分钟，使用场景：{scene}，操作人为：{operUser}，资产为：{devNamelp}'

export default {
  name: 'smsIndex',
  mixins: [baseHeader, baseTable, formDialog],
  data () {
    return {
      list: {
        showIndex: false,
        columns,
        api: 'fetchSmsTplListPage',
        params: {
          tplType: 1
        },
        showSelection: true,
        handleColumn: [
          {
            type: 'Redact',
            name: '编辑',
            icon: 'bianji'
            // disabled: !this._checkPermission('/area', 'PUT')
          }
        ],
        handleColumnProp: {
          label: '操作',
          align: 'left',
          width: '120px'
        }
      },
      headers: {
        buttons: [],
        title: '',
        showAlertIcon: false
        // content: '操作说明'
      },
      addFormLoading: false,
      addForm: {
        mesTitle: '',
        mesContent: ''
      },
      addFormItems: [
        {
          prop: 'mesTitle',
          label: '模板名称',
          placeholder: '请输入模板名称',
          maxlength: 50
        },
        {
          autosize: { minRows: 5 },
          type: 'textarea',
          prop: 'mesContent',
          label: '模板内容',
          placeholder: smsTemplate,
          maxlength: 50
        }
      ],
      dialogButtons: [
        {
          text: '保存',
          type: 'Submit'
        }
      ]
    }
  },
  computed: {
    url () {
      const env = process.env.VUE_APP_ENV
      let host = this.$path || window.location.protocol + '//' + window.location.host
      let protocol = host.indexOf('https') > -1 ? 'https' : 'http'
      //   let orderUrl = protocol + '://192.168.0.200:9998'
      let orderUrl = protocol + (env === 'dev' ? '://order.yun.jeecms.com' : '://order.yun.jeecms.com')
      /* system-cms-prefix start */
      let prefix = 'cmsmanager'
      /* system-cms-prefix change let prefix = 'cmsadmin' system-cms-prefix change */
      /* system-cms-prefix end */
      // siteId 站点id    siteName 站点名称   type: 判断是否是短息服务类型    ShowHide: 判断是否有返回按钮
      let src = orderUrl + '/#/service/apply/index?token=' + localStorage.getItem('JEECMS-Auth-Token') + '&url=' + host + '&prefix=' + prefix + '&siteId=' + localStorage.getItem('siteId') + '&siteName=' + localStorage.getItem('siteName') + '&type=' + '短信服务' + '&ShowHide=' + true +
        '&userName=' +
        localStorage.getItem('userName')
      return src
    }
  },
  methods: {
    fetchTableCallBack (res) {
      if (res.code === 200) {
        this.list.data = res.data.content
        this.list.totalCount = res.data.totalElements
      }
    },
    handleHeaderCreate () {
      this.$refs.editSmsDialog.showDialog()
    },
    // 编辑
    handleTableRedact (row) {
      this.dialogTitle = '编辑区域'
      this.id = row.id
      this.detailRecord = []
      this.$request.fetchSystemSmsTempDetails(row).then(res => {
        if (res.code === 200) {
          this.detailRecord = res.data
          this.addForm = res.data.details.find(item => item.mesType === 3)
          this.$refs.editSmsDialog.showDialog()
        }
      })
    },
    handleConfirmAdd (data, btn) {
      this.detailRecord.details.forEach(item => {
        if (item.id === data.id) {
          item.mesTitle = data.mesTitle
          item.mesContent = data.mesContent
        }
      })
      this.$request.fetchSmsTplUpdate(this.detailRecord).then(res => {
        if (res && res.code === 200) {
          this.$message({
            message: '修改成功',
            type: 'success'
          })
          this.fetchTableApi()
        }
      })
    }
  },
  created () {
  },
  watch: {
  }
}
</script>

<style>
.service-index {
  width:100%;
  height:100%;
}
</style>
<style lang="scss" scoped>
// .system-setting-container{
//   font-size: 16px;
//   header{
//     padding-bottom: 20px;
//     span{
//       padding-right: 10px;
//     }
//   }
//   .el-alert{
//     margin-bottom: 20px;
//   }
// }

</style>

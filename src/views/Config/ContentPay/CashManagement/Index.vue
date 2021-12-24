<template>
    <section class="statistics_detail">
        <el-tabs v-model="activeTab">
            <el-tab-pane :label="tabs[0].label" :name="tabs[0].name">
                <Audit :activeTab="activeTab"></Audit>
            </el-tab-pane>
            <el-tab-pane :label="tabs[1].label" :name="tabs[1].name">
               <Success :activeTab="activeTab"></Success>
            </el-tab-pane>
            <el-tab-pane :label="tabs[2].label" :name="tabs[2].name">
                <Failed :activeTab="activeTab"></Failed>
            </el-tab-pane>
        </el-tabs>
    </section>
</template>
<script>
import Audit from './audit'
import Success from './success'
import Failed from './failed'

export default {
    name:"configCashManagement",
    components: {
       Audit,
       Success,
       Failed
    },
    data(){
      return{
            activeTab: '1',
            tabs: [
                {
                name: '1',
                label: '提现审核',
                component: 'Membership'
                },
                {
                name: '2',
                label: '提现成功',
                component: 'UserInfo'
                },
                {
                name: '3',
                label: '提现失败',
                component: 'tixian'
                }
            ]
        }
    }
}
</script>
<style lang="scss">

</style>
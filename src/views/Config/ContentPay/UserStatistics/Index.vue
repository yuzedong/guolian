<template>
    <section class="statistics_detail">
        <el-tabs v-model="activeTab">
            <el-tab-pane :label="tabs[0].label" :name="tabs[0].name">
                <balance :activeTab="activeTab"></balance>
            </el-tab-pane>
            <el-tab-pane :label="tabs[1].label" :name="tabs[1].name">
                <earnings :activeTab="activeTab"></earnings>
            </el-tab-pane>
            <el-tab-pane :label="tabs[2].label" :name="tabs[2].name">
                <withdraw :activeTab="activeTab"></withdraw>
            </el-tab-pane>
        </el-tabs>
    </section>
</template>
<script>
import Balance from './balance'
import Earnings from './earnings'
import Withdraw from './withdraw'

export default {
    name:"configPayStatisticsDetail",
    components: {
        Balance,
        Earnings,
        Withdraw
    },
    data(){
        return{
            activeTab: '1',
            tabs: [
                {
                name: '1',
                label: '按余额',
                component: 'Membership'
                },
                {
                name: '2',
                label: '按收益',
                component: 'UserInfo'
                },
                {
                name: '3',
                label: '按提现',
                component: 'tixian'
                }
            ]
        }
    }
}
</script>
<style lang="scss">

</style>
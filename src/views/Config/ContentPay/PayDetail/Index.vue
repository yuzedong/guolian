<template>
    <section class="statistics_detail">
         <base-header v-bind="headers"/>
        <el-tabs v-model="activeTab">
            <el-tab-pane :label="tabs[0].label" :name="tabs[0].name">
                <Content :activeTab="activeTab" :channelId="channelId"></Content>
            </el-tab-pane>
            <el-tab-pane :label="tabs[1].label" :name="tabs[1].name">
                <Column :activeTab="activeTab" @retrieve="retrieve"></Column>
            </el-tab-pane>
            <el-tab-pane :label="tabs[2].label" :name="tabs[2].name">
                <Order :activeTab="activeTab"></Order>
            </el-tab-pane>
        </el-tabs>
    </section>
</template>
<script>
import Content from './content'
import Column from './column'
import Order from './order'
import baseHeader from '@/components/mixins/baseHeader'

export default {
    name:"configPayDetail",
    mixins: [baseHeader],
    components: {
        Content,
        Column,
        Order
    },
    data(){
      return{
        headers: {
            buttons: [],
            title: '操作说明',
            showAlertIcon: true,
            content: '当内容或栏目被删除后，可能会影响按内容或栏目中统计数据的准确性，但不影响按订单中的数据。'
        },
        activeTab: '1',
        channelId:0,
        tabs: [
            {
            name: '1',
            label: '按内容',
            component: 'Membership'
            },
            {
            name: '2',
            label: '按栏目',
            component: 'UserInfo'
            },
            {
            name: '3',
            label: '按订单',
            component: 'tixian'
            }
        ]
      }

    },
    methods:{
        retrieve(data){
            this.activeTab = data.activeTab
            this.channelId = data.channelId
        }
    }

}
</script>
<style lang="scss">

</style>

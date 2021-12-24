<template>
  <div class="paystatistic-tendency-container">
    <v-chart v-bind="chartOption()" :height="tendencyHeight" :data="Data" :scale="scale">
      <v-tooltip />
      <v-axis v-bind="axisOption({dataKey:'statisticTime'})"/>
      <v-axis v-bind="axisOption({dataKey:'temperature'})"/>
      <v-legend v-bind="legendOption()"/>
      <v-smooth-line position="statisticTime*temperature" shape="smooth"
        :color="['money', colorOption(3)]"
      />
    </v-chart>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import {  getTime, getData, getNewTime } from '@/utils'

const DataSet = require('@antv/data-set')
export default {
  data () {
    return {
      Data: [],
      tendencyHeight: 350,
      scale: [{
        dataKey: 'statisticTime'
      }]
    }
  },
  props: {
    tendencyData: {
      type: Array,
      default: () => {
        return []
      }
    },
    tendencyFields: {
      type: Array,
      default: () => {
        return []
      }
    },
    selected:{
      type:Number,
      delete:4
    }
  },
  computed: {
    ...mapGetters('app', ['chartOption', 'axisOption', 'legendOption', 'colorOption'])
  },
  methods: {
    getTime,
    getData,
    getNewTime,
    getTendencyData () {
      if (this.tendencyData && this.tendencyFields) {
        let sourceData = this.tendencyData
        let dv = new DataSet.View().source(sourceData)
        let obj = {}
        obj.type = 'fold'
        obj.fields = this.tendencyFields
        obj.key = 'money'
        obj.value = 'temperature'
        dv.transform(obj)
        this.Data = dv.rows
      }
    }
  },
  mounted () {
    this.getTendencyData()
  },
  watch: {
    tendencyData () {
      
      if (this.selected==1) {
        this.tendencyData.forEach(item => {
          item.statisticTime =item.hours?item.hours+':00':item.statisticTime
        });
      }
      this.getTendencyData()
      
    },
    selected(){
      
    }
  }
}
</script>

<style lang="scss">
.paystatistic-tendency-container{
  .g2-legend{
    left: 100px!important;
    top: -55px!important;
    .g2-legend-list-item{
      i{
        position: relative;
      }
    }
  }
}
</style>


<template>
  <Wrapper :config="config" @onResize="onResize">
    <div class="chart" :id="chartId"></div>
  </Wrapper>
</template>
 
<script>
import * as Echarts from "echarts"
import Wrapper from "../Wrapper/index.vue"
import _ from "lodash"
export default {
  name: 'charts',
  components: {
    Wrapper
  },
  props: {
    config: {
      type: Object,
      require:() => {}
    },
  },
  data() {
    return {
      myChart: {},
      chart_data:{}
    };
  },
  created() {
    this.$nextTick(() => {
      this.initData(this.config)
    });
  },
  mounted() {
    
  },
  computed:{
    chartId(){
      return `chart${this.config.index}`
    },
    configNew(){
      return JSON.parse(JSON.stringify(this.config))
    }
  },
  watch: {
    configNew: {
      handler(val,oldVal) {
        this.initData(val,oldVal)
      },
      deep: true
    },
  },
  methods: {
    // 初始化数据
    initData(val,oldVal){
      if(val.params.x_fields.length > 0 && val.params.y_fields.length > 0){
        if(oldVal){
          const xdif = _(val.params.x_fields).xorWith(oldVal.params.x_fields, _.isEqual).isEmpty()  //判断数组对象是否相等
          const ydif = _(val.params.y_fields).xorWith(oldVal.params.y_fields, _.isEqual).isEmpty()
          // 判断数据类型是否改变，若改变增需要重新调用接口，若无则重新渲染图表
          if(xdif && ydif){
            this.loadEchart()
          }else{
            this.setData()
          }
        }else{
          this.getChartData()
          this.loadEchart()
        }
      }else{
        this.chart_data = {}
        this.loadEchart()
      }
    },
    // 自适应
    onResize(){
      this.myChart.resize()
    },
    // 获取参数
    getOptions(){
      if(this.config.type == 'barChart'){
        return this.barOptions()
      }else if(this.config.type == 'lineChart'){
        return this.lineOptions()
      }else if(this.config.type == 'pieChart'){
        return this.pieOptions()
      }else if(this.config.type == 'gaugeChart'){
        return this.gaugeOptions()
      }
      return null 
    },
    // 设置配置
    setSeries(_type){
      const params = this.configNew.params
      let defaultSeries =  [{name: "销量",data:[5, 20, 36, 10, 10, 20]}]
      if(this.chart_data.series){
        defaultSeries = this.chart_data.series
      }
      let series = []
      defaultSeries.forEach(e =>{
        let _markLineData = [] 
        if(params.refer_line_switch && params.refer_line.length > 0){
          params.refer_line.forEach(line =>{
            if(line.name != '' && line.type != ''){
              let data = {}
              if(line.type == 'fixed' ){
                data = {yAxis:line.target_fixed}
              }else{
                if(e.id != line.target) return
                data = {type: line.type}
              }
              _markLineData.push({
                ...data,
                name: line.name,
                lineStyle: { color: "#f29d38"},
                label: { formatter: line.name,fontSize:line.size != '' ? line.size : '12' },
              })
            }
          })
        }
        series.push({
          name: e.name,
          type: _type,
          data: e.data,
          markLine: {
            symbol: "none", //标线箭头取消
            data: _markLineData,
            label: {color :"#666"},
            lineStyle: {
              type: "solid",
              with: 1,
            },
          },
        })
      })
      return series
    },
    // 柱状图参数
    barOptions(){
      const params = this.configNew.params
      const options = { 
        tooltip: {},
        grid:{
          top: '2%',
          bottom:'22%'
        },
        xAxis: {
          name: params.xAxis_switch && params.xAxis_input != ''? params.xAxis_input : false,
          data: this.chart_data.xAxis ? this.chart_data.xAxis : ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"],
          nameTextStyle:{
            fontSize: params.xAxis_switch && params.xAxis_size != ''? params.xAxis_size : 12,
            padding:[15,0,0,0],
          },
          nameLocation:'center',
          axisLabel: {
            show: true,
            textStyle: {
              color: "#333",
            },
          },
        },
        yAxis: {
          name:params.yAxis_switch && params.yAxis_input != ''? params.yAxis_input : false,
          // nameRotate:90,
          nameTextStyle:{
            fontSize: params.yAxis_switch && params.yAxis_size != ''? params.yAxis_size : 12,
            padding:[0,0,10,0],
          },
          nameLocation:'center',
          axisLabel: {
            show: true,
            textStyle: {
              color: "#333",
            },
          },
        },
        axisLabel: {
          show: true,
          textStyle: {
            color: "#333",
            fontSize: 12,
          },
        },
        series: this.setSeries('bar')
      }
      return options
    },
    // 折线图参数
    lineOptions(){
      const params = this.configNew.params
      const options = {
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            label: {
              backgroundColor: "#6a7985",
            },
          },
        },
        grid: {
          top: '2%',
          left:'7%',
          bottom:'16%',
          containLabel: true,
        },
        xAxis: [
          {
            name: params.xAxis_switch && params.xAxis_input != ''? params.xAxis_input : false,
            type: "category",
            data: this.chart_data.xAxis ? this.chart_data.xAxis : ["周一", "周二", "周三", "周四", "周五", "周六", "周日"],
            nameTextStyle:{
              fontSize: params.xAxis_switch && params.xAxis_size != ''? params.xAxis_size : 12,
              padding:[15,0,0,0],
            },
            nameLocation:'center',
            axisLabel: {
              show: true,
              textStyle: {
                color: "#333",
              },
            },
          },
        ],
        yAxis: [
          {
            name:params.yAxis_switch && params.yAxis_input != ''? params.yAxis_input : false,
            nameTextStyle:{
              fontSize: params.yAxis_switch && params.yAxis_size != ''? params.yAxis_size : 12,
              padding:[0,0,10,0],
            },
            nameLocation:'center',
            type: "value",
            axisLabel: {
              show: true,
              textStyle: {
                color: "#333",
              },
            },
          },
        ],
        series: this.setSeries('line')
      }
      return options
    },
    // 饼图参数
    pieOptions(){
      // const params = this.configNew.params
      let _series =  [{name: "访问来源",data: [{ value: "11", name: "交房" },{ value: "22", name: "装修" },{ value: "33", name: "入住" },{ value: "44", name: "空置" }]}]
      if(this.chart_data.series){
        _series = this.chart_data.series
      }
      const options = {
        title: {
          left: "center",
        },
        tooltip: {
          trigger: "item",
          formatter: "{a} <br/>{b} : {c} ({d}%)",
        },
        series: [
          {
            name: _series[0].name,
            type: "pie",
            radius: "50%",
            center: ["50%", "45%"],
            data: _series[0].data
          },
        ],
      }
      return options
    },
    //仪表盘参数
    gaugeOptions(){
      const params = this.configNew.params
      const num = 10
      const min = params.gauge_min != '' ? params.gauge_min : 0
      const max = params.gauge_max != '' ? params.gauge_max : 100
      const splitColor = ( num - min ) / (max - min)
      const options = {
        series: [
          {
            type: 'gauge',
            center: ["50%", "45%"],
            min:min,
            max:max,
            splitNumber:10,
            itemStyle: {
              color: '#509af2',
              shadowColor: 'rgba(0,138,255,0.45)',
              shadowBlur: 10,
              shadowOffsetX: 2,
              shadowOffsetY: 2
            },
            progress: {
              show: true,
              roundCap: true,
              width: 18
            },
            axisLine: {
              roundCap: true,
              lineStyle: {
                width: 18,
                color: [
                  [splitColor, '#509af2'],
								  [1, '#dcdcdc']
                ]
              }
            },
            axisTick: {
              show: false,
            },
            splitLine: {
              show: false,
              lineStyle: {
                width: 2,
                color: '#509af2'
              }
            },
            axisLabel: {
              distance: 0,
              color: '#999',
              fontSize: 16,
              formatter: function (value) {
                if(Math.round(value) === value){
                  return value
                }else{
                  return value.toFixed(2) 
                }
              },
            },
            title: {
              show: false
            },
            detail: {
              // valueAnimation: true,
              color: '#666',
              fontSize: 18,
              offsetCenter: [0, '70%'],
              formatter: function (value) {
                return '销量：' + value.toFixed(0) 
              },
            },
            data: [
              {
                value: num
              }
            ]
          }
        ]
      };
      return options
    },
    // 加载图形
    loadEchart() {
      this.myChart = Echarts.init(document.getElementById(this.chartId));
      this.myChart.setOption(this.getOptions(),true);
    },
    // 请求接口获取图标数据
    setData(){
      this.myChart.showLoading()
      setTimeout(()=>{
       this.getChartData('request')
      },1000)
    },
    // 模拟数据
    getChartData(type){
      const name = this.config.params.x_fields[0].name
      const yFields = this.config.params.y_fields
      const xAxis =  [name+"1", name+"2", name+"3", name+"4"]
      type == 'request' && this.myChart.hideLoading()
      yFields.forEach((e,index) => {
        if(this.config.type == 'pieChart'){
          this.$set(e,'data',[{name:name+"1",value:55-(index*10)},{name:name+"2",value:11+(index*20)},{name:name+"3",value:111+(index*10)},{name:name+"4",value:21+(index*10)}])
        }else{
           this.$set(e,'data',[55-(index*10),11+(index*20),111+(index*10),21+(index*10)])
        }
      })
  
      // 最终需要的值
      this.chart_data = {xAxis:xAxis,series:yFields}
      this.loadEchart()
    }
  },
};
</script>
 
<style lang="scss" scoped>
  .chart{
    width: 100%;
    height: 100%;
  }
</style>
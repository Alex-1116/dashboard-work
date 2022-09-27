<template>
  <div class="dashboard-work-erea">
    <p class="tit"><span class="imp">筛选、搜索区域</span>请先选择筛选组件，再进行筛选配置</p>
    <div
      class="drag"
    >
      <grid-layout
        :margin=[20,20]
        :layout.sync="dataJson"
        :col-num="colNum"
        :row-height="2"
        :is-draggable="draggable"
        :is-resizable="resizable"
        :vertical-compact="true"
        :use-css-transforms="true"
      >
        <grid-item
          v-for="item in dataJson"
          class="drag_item"
          :class="[active == item.i ? 'active': '']"
          :static="item.static"
          :x="item.x"
          :y="item.y"
          :w="item.w"
          :h="item.h"
          :key="item.i"
          :i="item.i"
          @moved="movedEvent"
        > 
        <div class="oper">
          <span @click="copyCard(item)">复制</span>
          <span>刷新</span>
          <span @click="removeCard(item.i)">删除</span>
        </div>
        <div class="box" @click="setActive(item)">
          <component :is="item.type" :config="item.config" :active="active"/>
        </div>
        </grid-item>
      </grid-layout>
    </div>
  </div>
</template>
<script>
import _ from 'lodash'
import { GridLayout, GridItem } from "vue-grid-layout"
import chart from '../Charts/index.vue'
export default {
  components: {
    GridLayout,
    GridItem,
    chart
  },
  data() {
    return { 
      colNum: 10,
      dataJson:[],                     //数据源
      draggable: true,
      resizable: true,
      index: 0,
      active: null,                    //当前选中的卡片序号
      curCard: null,                   //当前选中的卡片信息
      defaultParams: {
        data_sheet: '',                //报表数据来源类型1
        data_sheet2: '',               //报表数据来源类型2
        all_fields:[],                 //所有字段
        x_fields:[],                   //横轴/分类指标字段
        y_fields:[],                   //纵轴/数值指标字段
        filter_switch: false,          //字段过滤开关
        sort_switch:false,             //字段排序开关
        filter_fields:'',              //字段过滤 选择字段
        filter_fixed_fields:'',        //字段过滤 固定字段
        sort_fields:'',                //字段排序
        sort_type: 'desc',             //字段排序 固定字段
        limit_num:'',                  //字段排序 显示条数
        xAxis_switch:false,            //横轴标题开关
        xAxis_input:'',                //横轴标题内容
        xAxis_size:'',                 //横轴标题尺寸
        yAxis_switch:false,            //纵轴标题开关 
        yAxis_input:'',                //纵轴标题尺寸
        yAxis_size:'',                 //纵轴标题开关 
        gauge_min: '',                 //仪表盘最小值 
        gauge_max: '',                 //仪表盘最大值 
        name: '',                      //图表标题
        name_tip:'',                   //标题提示
        name_size:'',                  //标题尺寸
        name_link_switch:false,        //跳转链接开关
        name_link_type:'1',            //跳转链接类型
        name_link:'',                  //跳转链接地址
        refer_line_switch:false,       //参考线开关
        refer_line:'',                 //参考线配置
        currency_conver:'',            //币别转换配置
      }
    }
  },
  props:{
  },
  watch: {
    dataJson: {
      handler(val) {
        if(val.length == 0){
          this.active = null
          this.curCard = null
        }else{
          const arr = _.filter(val, e =>{
            return e.i == this.active 
          })
          if(arr.length == 0) {
            this.active = null,
            this.curCard = null
          }
        }
      },
      deep: true
    },
    active: {
      handler(val) {
        this.$emit('curCard', this.curCard)
      },
      deep: true
    },

  },
  methods: {
    // 设置选中
    setActive(item){
      this.active = item.i
      this.curCard = item
    },
    // 添加卡片
    addCard(val) {
      let name = val.indexOf('Chart') > -1 ?  'chart' : val
      let data = {
        x: this.dataJson.length %2==0 ? 0 : 5,
        y: 0,
        w: 5,
        h: 20,
        i: this.index,
        type: name,
        config: {'index':this.index,'type': val, 'params':this.defaultParams}
      };
      this.dataJson.push(data)
      this.active = this.index
      this.curCard = data
      this.index++
    },
    // 复制卡片
    copyCard(item){
      const arr = _.cloneDeep(item)
      const maxData = this.dataJson.reduce(function(prev, current) {
        return (prev.i > current.i) ? prev : current
      })
      arr.i = +maxData.i+1
      arr.x = 5
      arr.y = 5
      arr.config.index = +maxData.config.index+1
      this.dataJson.push(arr)
      this.index = maxData.i + 2
    },
    // 移除卡片
    removeCard(val) {
      const index = this.dataJson.map(item => item.i).indexOf(val)
      this.dataJson.splice(index, 1)
    },
    // 用户设置组件参数的变化值
    setParams(val,params){
      const cur = _.filter(this.dataJson, item=>{
       return item.config.index == val
      })
      if(cur){
        cur[0].config.params = {...cur[0].config.params, ...params}
      }
      this.$emit('curParams',cur[0].config.params)
    },
    movedEvent(){
      // console.log('this.dataJson', this.dataJson)
    }
  },
  mounted() {
    
  },
};
</script>

<style lang="scss" scoped>
  @import '../../style.scss';
 .dashboard-work-erea{
  background: #fff;
  margin: 12px 0 0 0;
  padding: 10px;
  min-height: calc(100% - 152px);
  .tit{
    font-size: 12px;
    color: #999;
    display: flex;
    justify-content: flex-start;
    .imp{
      color: #333;
      margin-right: 10px;
    }
  }
  .drag_item {
    position: relative;
    // overflow: hidden;
    box-shadow: 0 0 4px 2px #e9e9e9;
    border: 1px solid #d7d6d6;
    border-radius: 4px;
    &.active{
      border: 1px solid blue;
      .oper{
        display: block;
      }
    }
    .box{
      width: 100%;
      height: 100%;
    }
    .oper{
      font-size: 12px;
      position: absolute;
      right: 0;
      top: -15px;
      cursor: pointer;
      display: none;
      span{
        margin: 0 2px;
        color: $main;
      }
    }
  }
}
</style>
<template>
  <div class="dashboard-work-oper">
    <el-tabs v-model="activeTab" v-if="curCard">
      <el-tab-pane  
        v-for="item in tabs"
        :name="item.name"
        :label="item.label"
        :key="item.name">
        <component :is="item.name" :cur="curCard" :curParams="curParams" @changeParams="changeParams"/>
      </el-tab-pane>
    </el-tabs>
    <p v-else class="tips">请先选择需要操作的区域</p>
  </div>
</template>

<script>
import _ from 'lodash'
import target from './target.vue'
import page from './style.vue'
import other from './other.vue'
export default {
  components: {
    target,
    page,
    other,
  },
  props: {
    curCard: {
      type: Object,
      default:() => {}
    }
  },
  data() {
    return { 
      tabs:[
        {label:'报表指标', name:"target"},
        {label:'页面样式', name:"page"},
        {label:'其他', name:"other"}
      ],
      activeTab:'target',
      curParams:{}
    }
  },
  methods: {
    // 设置参数
    changeParams(val, params){
      this.$emit('changeParams',val, params)
    },
    // 获取参数
    getParams(val){
      this.curParams = val
    }
  },
  mounted() {
  },
};
</script>

<style lang="scss" scoped>
  @import '../../style.scss';
 .dashboard-work-oper{
   background: #fff;
  margin: 12px 0 12px 12px;
  padding: 0 10px;
  height: calc(100% - 12px);
  font-size: 12px;
  position: relative;
  .tips{
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }
}
</style>
<style lang="scss">
.dashboard-work-oper{
  .el-tabs{
    height: calc(100% - 60px); 
    // overflow: auto;
    .el-tabs__content{
      height: calc(100% - 10px);
      .el-tab-pane{
        height: 100%;
      }
    }
    .el-tabs__nav{
      width: 100%;
      .el-tabs__item{
        width: 33.3%;
        font-size: 12px;
        padding: 0 !important;
        text-align: center;
      }
    }
  }
}
</style>
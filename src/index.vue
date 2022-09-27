<template>
  <div class="dashboard-box">
    <Header ref="header"/>
    <div class="work-box">
      <div class="main">
        <SelectGroup ref="selectGroup" @selectGroup="selectGroup" />
        <div class="area scrollbar" :style="areaStyle">
          <SelectArea ref="selectArea" />
          <WorkArea ref="workArea" @curCard="setCurCard" @curParams="curParams"/>
        </div>
      </div>
      <div class="oper">
        <WorkOper ref="workOper" :curCard="curCard" @changeParams="changeParams"/>
      </div>
    </div>
  </div>
</template>

<script>
import { GridLayout, GridItem } from "vue-grid-layout"
import Header from './components/Header/index.vue'
import SelectGroup from './components/SelectGroup/index.vue'
import SelectArea from './components/SelectArea/index.vue'
import WorkArea from './components/WorkArea/index.vue'
import WorkOper from './components/WorkOper/index.vue'
import _ from 'lodash'
export default {
  components: {
    GridLayout,
    GridItem,
    Header,
    SelectGroup,
    SelectArea,
    WorkArea,
    WorkOper
  },
  data() {
    return {
      curCard:null,
      areaStyle : {}
    };
  },
  mounted() {
    this.setAreaStyle()
    window.addEventListener('resize', this.resizeAreaStyle)
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.resizeAreaStyle)
  },
  methods: {
    resizeAreaStyle: _.debounce(function() {
      let that = this
      that.setAreaStyle()
    }, 10),
    // 设置工作区域高度
    setAreaStyle(){
      this.$nextTick(()=>{
        const h = this.$el.getBoundingClientRect().height - this.$refs.header.$el.getBoundingClientRect().height - this.$refs.selectGroup.$el.getBoundingClientRect().height - 45
        this.areaStyle = {height:h+"px"}
      })
    },
    // 选择组件后添加卡片
    selectGroup(val){
      this.$refs.workArea.addCard(val)
    },
    // 设置当前选择元素
    setCurCard(val){
      this.curCard = val
    },
    // 改变参数
    changeParams(val,params){
      this.$refs.workArea.setParams(val,params)
    },
    // 传值参数
    curParams(val){
      this.$refs.workOper.getParams(val)
    }
  }
};
</script>

<style lang="scss" scoped>
  @import './style.scss';
  .dashboard-box{
    background: #eff0f4;
    padding: 10px;
    position: absolute;
    width: 100%;
    height: 100%;
    box-sizing: border-box;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    .work-box{
      display: flex;
      height: calc(100% - 50px);
      .main{
        width: calc(100% - 440px);
        height: 100%;
        // border: 1px solid #000; 
      }
      .oper{
        width: 440px;
        height: 100%;
        // border: 1px solid #000;
      }
      .area{
        overflow: auto;
      }
    }
  }
</style>
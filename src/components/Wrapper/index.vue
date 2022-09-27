
<template>
  <ResizeObserver @resize="onResize">
    <div class="card">
      <div class="name">
        <span :style="myStyle">
          <b v-show="config.params.name != ''">{{config.params.name}} </b> 
          <el-tooltip v-show="config.params.name_tip!= ''"  class="item" effect="light"  placement="top">
            <div slot="content">{{config.params.name_tip}}</div>
            <i class="el-icon-warning-outline"></i>
          </el-tooltip>
        </span>
        <a class="more" v-show="config.params.name_link_switch" :target="config.params.name_link_type == 1 ? '_blank' : '_self'" :href="config.params.name_link">更多</a>
        </div>
        <div class="box">
            <slot />
        </div>
    </div>
  </ResizeObserver>
</template>
 
<script>
import ResizeObserver from "../ResizeObserver/index.vue"
export default {
  components: {
    ResizeObserver
  },
  props: {
    config: {
      type: Object,
      require:() => {}
    },
  },
  data() {
    return {
      
    };
  },
  created() {
    
  },
  mounted() {
    
  },
  computed:{
    myStyle(){
      const size = this.config.params.name_size == '' ?  '12': this.config.params.name_size
      return {fontSize: size+'px'}
    }
  },
  watch: {
    
  },
  methods: {
    onResize(){
      this.$emit('onResize')
    }
  },
};
</script>

<style lang="scss" scoped>
  @import '../../style.scss';
  .card{
    width: 100%;
    height: 100%;
    position: relative;
    .box {
      width: 100%;
      height: calc(100% - 65px);
    } 
    .name {
      font-size: 12px;
      height: 24px;
      line-height: 24px;
      margin: 20px;
      width: calc(100% - 40px);
      z-index: 1;
      @include flex($jc: space-between, $alignI: center);
      cursor: pointer;
      .more{
        color: $main;
        font-size: 12px;
        &:hover{
          color: $main-hover;
        }
      }
    } 
  }
</style>
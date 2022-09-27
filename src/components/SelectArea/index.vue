<template>
  <div class="dashboard-select-erea">
    <p class="tit"><span class="imp">图表展示区域</span>请先选择顶部的图表类型，然后点击图表进行图表指标设置</p>
  </div>
</template>

<script>
import _ from 'lodash'
export default {
  data() {
    return { 
    }
  },
  methods: {
  
  
  
  },
  mounted() {
  },
};
</script>

<style lang="scss" scoped>
  @import '../../style.scss';
 .dashboard-select-erea{
  background: #fff;
  margin: 0 0 12px 0;
  padding: 10px;
  height: 100px;
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
}
</style>
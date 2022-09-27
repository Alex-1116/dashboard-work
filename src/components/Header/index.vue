<template>
  <div class="header">
    <span></span>
    <ul class="step">
      <li class="active"><i class="dot"></i>基础设置</li>
      <li><i class="dot"></i>页面权限</li>
    </ul>
    <div>
      <el-button size="mini">下一步</el-button>
      <el-button size="mini">保存</el-button>
      <el-button size="mini">取消</el-button>
      <el-button size="mini">预览</el-button>
    </div>
  </div>

</template>

<script>
import _ from 'lodash'
export default {
  data() {
    return {
    };
  },
  methods: {
  },
  mounted() {
  },
};
</script>

<style lang="scss" scoped>
  @import '../../style.scss';
 .header{
  background: #fff;
  border-radius: 4px;
  padding: 10px 15px;
  @include flex($jc: space-between, $alignI: center);
  .step{
     display: flex;
     list-style:none;
     li{
      font-size: 12px;
      margin: 0 40px;
      position: relative;
      display: flex;
      justify-content: flex-start;
      align-items: center;
      .dot{
        width: 16px;
        height: 16px;
        // background: #f29d38;
        border: 1px solid #999;
        border-radius: 50%;
        position: relative;
        margin-right: 5px;
        // position: absolute;
        // left: -25px;
        // top: -5px;
      }
      &.active{
        font-weight: bold;
        // &:before{
          // background: #fff;
          // border-color: #f29d38;
        // }
        .dot{
          background: #fff;
          border-color: $main;
          &:after{
            content: '';
            width: 12px;
            height: 12px;
            background: $main;  
            border-radius: 50%;
            position: absolute;
            left: 2px;
            top: 2px;
          }
        }
      }
      &:not(:first-child){
        &:before{
          content: '';
          width: 80%;
          height: 1px;
          background: #d4d4d4;
          position: absolute;
          left: -70px;
          top: 9px
        }
       
      }
     }
  }
 }
</style>
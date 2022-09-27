<template>
  <div class="dashboard-select-box">
    <div>
      <el-dropdown @command="handleCommand" placement="bottom-start">
        <span class="el-dropdown-link">
          图表<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item
            v-for="item in options1"
            :command="item.value"
            :key="item.value">
            {{item.label}}
          </el-dropdown-item>
        </el-dropdown-menu>
        </el-dropdown>
      <el-dropdown @command="handleCommand" placement="bottom-start">
        <span class="el-dropdown-link">
          指标卡<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item
            v-for="item in options2"
            :key="item.value"
            :command="item.value">
            {{item.label}}
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <el-dropdown @command="handleCommand" placement="bottom-start">
        <span class="el-dropdown-link">
          报表<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item
            v-for="item in options3"
            :key="item.value"
            :command="item.value">
            {{item.label}}
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <el-dropdown @command="handleCommand" placement="bottom-start">
        <span class="el-dropdown-link">
          基础<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item
            v-for="item in options4"
            :key="item.value"
            :command="item.value">
            {{item.label}}
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <el-dropdown @command="handleCommand" placement="bottom-start">
        <span class="el-dropdown-link">
          工作台<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item
            v-for="item in options5"
            :key="item.value"
            :command="item.value">
            {{item.label}}
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <el-dropdown @command="handleCommand" placement="bottom-start">
        <span class="el-dropdown-link">
        筛选<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item
            v-for="item in options6"
            :key="item.value"
            :command="item.value">
            {{item.label}}
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>
    <el-button size="mini">大屏属性</el-button>
  </div>
</template>

<script>
import _ from 'lodash'
export default {
  data() {
    return {
      options1: [{
        value: 'barChart',
        label: '柱状图'
      }, {
        value: 'lineChart',
        label: '折线图'
      }, {
        value: 'pieChart',
        label: '饼图'
      }, {
        value: 'gaugeChart',
        label: '仪表盘'
      }],
      options2: [{
        value: 'card',
        label: '基础指标卡'
      }],
      options3: [{
        value: 'table',
        label: '基础报表'
      }],
      options4: [{
        value: 'tab',
        label: '选项卡'
      }, {
        value: 'text',
        label: '文本'
      }],
      options5: [{
        value: 'notice',
        label: '通知/代办事项'
      }],
      options6: [{
        value: 'select',
        label: '下拉筛选'
      }, {
        value: 'time',
        label: '时间筛选'
      }],
    }
  },
  methods: {
    handleCommand(val){
      this.$emit('selectGroup', val)
    }
  },
  mounted() {
  },
};
</script>

<style lang="scss" scoped>
  @import '../../style.scss';
 .dashboard-select-box{
   background: #fff;
   margin: 12px 0;
   padding: 10px;
   @include flex($jc: space-between, $alignI: center);
   .el-dropdown{
     font-size: 12px;
     margin-right: 20px;
     cursor: pointer;
     &:hover{
       color: #1682e6;
     }
   }
 }
</style>
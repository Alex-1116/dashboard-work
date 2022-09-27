<template>
  <div class="dashboard-style-main scrollbar">
    <div class="dashboard-style">
      <p class="tit">
        字段过滤与排序
        <el-tooltip class="item" effect="light"  placement="top">
          <div slot="content" style="width:300px;">可对图表展示的字段进行筛选设置。<br><br>例如你想查看xx站销量前10的商品。首先选择“国家”字段，然后固定字段选择“xx国”；其次字段排序，选择“销量”字段，选择“降序”，显示前10条数据即可满足该需求。</div>
          <i class="el-icon-warning-outline"></i>
        </el-tooltip>
      </p>
      <p class="msg">
        <span class="middle">字段过滤 </span>
        <el-switch
          class="middle"
          v-model="params.filter_switch"
          :active-color="active_color"
          @change="(bol) => changeParams(bol)">
        </el-switch>
      </p>
      <div class="card">
        <p>选择字段：
          <el-select :disabled="!params.filter_switch" v-model="params.filter_fields" placeholder="请选择" size="mini" style="width:calc(100% - 80px); " @change="changeParams">
            <el-option
              v-for="item in filter_options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </p>
        <p>固定字段：
          <el-select :disabled="!params.filter_switch" v-model="params.filter_fixed_fields" placeholder="请选择" size="mini" style="width:calc(100% - 80px); " @change="changeParams">
            <el-option
              v-for="item in filter_fixed_options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </p>
      </div>
      <p class="msg">
        <span class="middle">字段排序 </span>
        <el-switch
          class="middle"
          v-model="params.sort_switch"
          :active-color="active_color"
          @change="(bol) => changeParams(bol)">
        </el-switch>
      </p>
      <div class="card">
        <p>字段排序：
          <el-select :disabled="!params.sort_switch" v-model="params.sort_fields" placeholder="请选择" size="mini" style="width:calc(100% - 80px); " @change="changeParams">
            <el-option
              v-for="item in sort_options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </p>
        <p>固定字段：
          <el-select :disabled="!params.sort_switch" v-model="params.sort_type" placeholder="请选择" size="mini" style="width:calc(100% - 80px); " @change="changeParams">
            <el-option
              v-for="item in sort_type_options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </p>
        <p>显示前 <el-input :disabled="!params.sort_switch" v-model="params.limit_num" type="number" size="mini" style="width:50px; margin: 0 5px" @input="changeParams"/> 条数据</p>
      </div>
    </div>
    <div class="dashboard-style" v-if="['barChart','lineChart'].includes(cur.config.type)">
      <p class="tit">
        坐标轴设置
      </p>
       <p class="msg">
         <span class="middle">横轴标题 </span>
         <el-switch
           class="middle"
           v-model="params.xAxis_switch"
           :active-color="active_color"
           @change="(bol) => changeParams(bol)">
         </el-switch>
        </p>
        <el-input :disabled="!params.xAxis_switch" v-model="params.xAxis_input" @input="(val) => changeParams(val)" size="mini" maxLength="20" placeholder="请输入标题内容"/>
        <p class="msg">标题尺寸</p>
        <el-select :disabled="!params.xAxis_switch" v-model="params.xAxis_size" placeholder="请选择" size="mini" style="width:100%;" @change="changeParams">
          <el-option
            v-for="item in xAxis_options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
        <p class="msg">
        <span class="middle">纵轴标题 </span>
          <el-switch
            class="middle"
            v-model="params.yAxis_switch"
            :active-color="active_color"
            @change="(bol) => changeParams(bol)">
          </el-switch>
       </p>
       <el-input :disabled="!params.yAxis_switch" v-model="params.yAxis_input" @input="(val) => changeParams(val)" size="mini" maxLength="20" placeholder="请输入标题内容"/>
       <p class="msg">标题尺寸</p>
       <el-select :disabled="!params.yAxis_switch" v-model="params.yAxis_size" placeholder="请选择" size="mini" style="width:100%;" @change="changeParams">
        <el-option
          v-for="item in yAxis_options"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
    </div>
    <div class="dashboard-style" v-else-if="['gaugeChart'].includes(cur.config.type)">
      <p class="tit">
        刻度设置
        <el-tooltip class="item" effect="light"  placement="top">
          <div slot="content" style="width:100px;">设置仪表盘的区间最大值和最小值</div>
          <i class="el-icon-warning-outline"></i>
        </el-tooltip>
      </p>
      <p class="msg">
         <span class="middle">最小值：</span>
         <el-input v-model="params.gauge_min" type="number" size="mini" style="width:calc(100% - 50px);" placeholder="请输入" @input="changeParams"/>
      </p>
      <p class="msg">
         <span class="middle">最大值：</span>
         <el-input v-model="params.gauge_max" type="number" size="mini" style="width:calc(100% - 50px);" placeholder="请输入" @input="changeParams"/>
      </p>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    cur: {
      type: Object,
      default:() => {}
    }
  },
  watch: {
    cur: {
      handler(val) {
        Object.keys(this.params).forEach(key => {
          this.params[key] = val.config.params[key]
        })
      },
      immediate:true
    },
  },
  data() {
    return { 
      active_color:'#f29d38',
      filter_options:[{value: 'sku',label: 'SKU'}, {value: 'country',label: '国家'}, {value: 'nums',label: '销量'}, {value: 'sale',label: '销售额'}],
      filter_fixed_options:[{value: 'america',label: '美国'}, {value: 'britain',label: '英国'}, {value: 'germany',label: '德国'}, {value: 'japan',label: '日本'}],
      sort_options:[{value: 'sku',label: 'SKU'},{value: 'nums',label: '销量'}, {value: 'sale',label: '销售额'}],
      sort_type_options:[{value: 'desc', label:'降序（默认）'},{value: 'asc' , label: '升序'}],
      xAxis_options:[{value: '12', label:'12'},{value: '14', label:'14'},{value: '16', label:'6'},{value: '18', label:'18'},{value: '20', label:'20'},{value: '22', label:'22'},{value: '24', label:'24'}],
      yAxis_options:[{value: '12', label:'12'},{value: '14', label:'14'},{value: '16', label:'6'},{value: '18', label:'18'},{value: '20', label:'20'},{value: '22', label:'22'},{value: '24', label:'24'}],
      params:{
        filter_switch: false,   //字段过滤开关
        sort_switch:false,      //字段排序开关
        filter_fields:'',       //字段过滤 选择字段
        filter_fixed_fields:'', //字段过滤 固定字段
        sort_fields:'',         //字段排序
        sort_type: 'desc',      //字段排序 固定字段
        limit_num:'',           //字段排序 显示条数
        xAxis_switch:false,     //横轴标题开关
        xAxis_input:'',         //横轴标题内容
        xAxis_size:'',          //横轴标题尺寸
        yAxis_switch:false,     //纵轴标题开关 
        yAxis_input:'',         //纵轴标题尺寸
        yAxis_size:'',          //纵轴标题开关 
        gauge_min: '',          //仪表盘最小值 
        gauge_max: '',          //仪表盘最大值 
      }
    }
  },
  methods: {
    // 传值
    changeParams(){
      this.$emit('changeParams',this.cur.i, this.params)
    },
  },
  mounted() {
  },
};
</script>
<style lang="scss" scoped>
  @import '../../style.scss';
  .dashboard-style-main{
    height: 100%;
    overflow: auto;
   .dashboard-style{
    padding: 0 15px 15px;
    margin-bottom: 10px;
    border-bottom: 1px dashed $border;
    color: #333;
    .tit{
      position: relative;
      padding-top: 5px;
      display: flex;
      justify-content: flex-start;
      align-items: center;
      &:after {
        content: '';
        width: 3px;
        height: 80%;
        background: $main;
        position: absolute;
        left: -10px;
        top: 5px;
      }
    }
    .msg{
      margin: 10px 0 5px;
      display: flex;
      justify-content: flex-start;
      align-items: center;
    }
    .card{
      margin: 5px 0 15px;
      line-height: 40px;
      border: 1px solid $border;
      padding: 10px;
      box-shadow: 0 0 4px 2px #e9e9e9;
      border-radius: 4px;
      &:last-child{
        margin-bottom: 0;
      }
      p{
        display: flex;
        justify-content: flex-start;
        align-items: center;
      }
    }
    ::v-deep .el-input__inner{
      padding: 0 0 0 10px
    }
   }
  }
</style>
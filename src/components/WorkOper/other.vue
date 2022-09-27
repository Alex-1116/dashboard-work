<template>
  <div class="dashboard-style-other scrollbar">
    <div class="dashboard-style">
      <p class="tit">图表标题设置</p>
      <div class="card">
        <p>图表标题：
          <el-input v-model="params.name" placeholder="请输入" size="mini" style="width:calc(100% - 80px); " maxlength="20" @input="changeParams"/>
        </p>
        <p>标题提示：
          <el-input  v-model="params.name_tip" placeholder="请输入" size="mini"  style="width:calc(100% - 80px); " maxlength="20" @input="changeParams"/>
        </p>
        <p>标题尺寸：
          <el-select v-model="params.name_size" placeholder="请选择" size="mini" style="width:calc(100% - 80px); " @change="changeParams">
            <el-option
              v-for="item in name_size_options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </p>
        <p>
          跳转链接
          <el-tooltip class="item middle" effect="light"  placement="top">
            <div slot="content">跳转链接提示</div>
            <i class="el-icon-warning-outline"></i>
          </el-tooltip>
          <el-switch
            class="middle ml10"
            v-model="params.name_link_switch"
            :active-color="active_color"
            @change="changeParams">
          </el-switch>
        </p>
        <p>
          <el-radio :disabled="!params.name_link_switch" v-model="params.name_link_type" label="1" @change="changeParams">打开新窗口</el-radio>
          <el-radio :disabled="!params.name_link_switch" v-model="params.name_link_type" label="2" @change="changeParams">当前页跳转</el-radio>
        </p>
        <p>
          <el-input :disabled="!params.name_link_switch" v-model="params.name_link" size="mini"  maxlength="20" style="width:calc(100% - 15px); "  placeholder="请输入跳转链接" @input="changeParams"/>
        </p>
      </div>
    </div>
    <div class="dashboard-style" v-if="['barChart','lineChart'].includes(cur.config.type)">
      <p class="tit">
        参考线设置
        <el-switch
          class="middle ml10"
          v-model="params.refer_line_switch"
          :active-color="active_color"
          @change="changeParams">
        </el-switch>
      </p>
      <div class="card-cont" v-for="(e, index) in refer_line" :key="index" >
        <div class="card">
          <p>
            <span class="name">参考线名称：</span>
            <el-input :disabled="!params.refer_line_switch" v-model="e.name" placeholder="请输入" size="mini"  style="width:calc(100% - 80px); " @input="changeParams"/>
          </p>
          <p>
            <span class="name">参考线尺寸：</span>
            <el-select :disabled="!params.refer_line_switch" v-model="e.size" placeholder="请选择" size="mini" style="width:calc(100% - 80px); " @change="changeParams">
              <el-option
                v-for="item in name_size_options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </p>
          <p>
            <span class="name">参考线类型：</span>
            <el-select :disabled="!params.refer_line_switch" v-model="e.type" placeholder="请选择" size="mini" style="width:calc(100% - 80px); " @change="changeParams">
              <el-option
                v-for="item in type_options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </p>
          <p v-show="e.type != '' && e.type != 'fixed'">
            <span class="name">参考线指标：</span>
            <el-select :disabled="!params.refer_line_switch" v-model="e.target" placeholder="请选择" size="mini" style="width:calc(100% - 80px); " @change="changeParams">
              <el-option
                v-for="item in target_options"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </p>
          <p v-show="e.type == 'fixed'">
            <span class="name">固定值：</span>
            <el-input :disabled="!params.refer_line_switch" v-model="e.target_fixed" size="mini"  type="number" style="width:calc(100% - 80px); "  placeholder="请输入" @input="changeParams"/>
          </p>
        </div>
        <span class="btn" :class="{'dis':!params.refer_line_switch}" v-if="index == 0" @click="addItem(1)">新增</span>
        <span class="btn" :class="{'dis':!params.refer_line_switch}" v-else @click="deleteItem(1,index)">删除</span>
      </div>
    </div>
    <div class="dashboard-style">
      <p class="tit">
        币别转换
        <el-tooltip class="item middle" effect="light"  placement="top">
          <div slot="content">“针对含有“货币符号+货币金额”的字段，在创建报表后按需进行货币的切换。</div>
          <i class="el-icon-warning-outline"></i>
        </el-tooltip>
        <el-switch
          class="middle ml10"
          v-model="params.currency_conver_switch"
          :active-color="active_color"
          @change="changeParams">
        </el-switch>
      </p>
      <div v-for="(e, index) in currency_conver" :key="index" class="mb10">
        <el-select :disabled="!params.currency_conver_switch" v-model="e.fields" placeholder="请选择字段" size="mini" style="width:100px" @change="changeParams">
          <el-option
            v-for="item in currency_conver_fields_options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
        <el-select :disabled="!params.currency_conver_switch || e.fields =='' " v-model="e.currency_fields" placeholder="对应货币字段" size="mini" style="width:calc(100% - 150px);margin-left:10px;" @change="changeParams">
          <el-option
            v-for="item in currency_conver_currency_fields_options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
        <span class="btn ml10" :class="{'dis':!params.currency_conver_switch}" v-if="index == 0" @click="addItem(2)">新增</span>
        <span class="btn ml10" :class="{'dis':!params.currency_conver_switch}" v-else @click="deleteItem(2,index)">删除</span>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    cur: {
      type: Object,
      default:() => {}
    },
    curParams:{
      type: Object,
      default:() => {}
    }
  },
  watch: {
    cur: {
      handler(val) {
        Object.keys(this.params).forEach(key => {
          this.params[key] = val.config.params[key]
          if(key == 'refer_line'){
            if( val.config.params[key] == ''){
              this.refer_line = [{name:'',size:'',type:'',target:'',target_fixed:''}]
            }else{
              this.refer_line = val.config.params[key] 
            }
          }else if(key == 'currency_conver'){
            if( val.config.params[key] == ''){
              this.currency_conver = [{fields:'', currency_fields:''}]
            }else{
              this.currency_conver = val.config.params[key] 
            }
          }
        })
        if(val.config.params.y_fields.length > 0){
          this.target_options = val.config.params.y_fields
        }
      },
      immediate:true
    },
    // 获取参考线指标
    curParams:{
      handler(val) {
        if(val.y_fields.length > 0){
          this.target_options = val.y_fields
        }
      },
      deep:true
    }
  },
  data() {
    return { 
      active_color:'#f29d38',
      name_size_options:[{value: '12',label: '12'}, {value: '14',label: '14'}, {value: '16',label: '16'}, {value: '18',label: '18'}, {value: '20',label: '20'},{value: '22',label: '22'},{value: '24',label: '24'}],
      type_options:[{value: 'fixed',label: '固定值'},{value: 'average',label: '平均值'},{value: 'max',label: '最大值'},{value: 'min',label: '最小值'}],
      target_options:[],
      currency_conver_switch:false,
      currency_conver_fields_options:[{value: 'sku',label: 'sku'},{value: 'sale',label: '售价'},{value: 'buy',label: '采购成本'}],
      currency_conver_currency_fields_options:[{value: '1',label: '字段备注'},{value: '2',label: '字段备注2'},{value: '3',label: '字段备注3'}],
      refer_line:[{
        name:'',        //参考线名称
        size:'',        //参考线尺寸
        type:'',        //参考线类型
        target:'',      //参考线指标
        target_fixed:'' //参考线固定值
        }
      ],
      currency_conver: [{fields:'', currency_fields:''}],
      params:{
        name: '',                      //图表标题
        name_tip:'',                   //标题提示
        name_size:'',                  //标题尺寸
        name_link_switch:false,        //跳转链接开关
        name_link_type:'1',            //跳转链接类型
        name_link:'',                  //跳转链接地址
        refer_line_switch:false,       //参考线开关
        refer_line:'',                 //参考线配置
        currency_conver:''             //币别转换配置
      }
    }
  },
  methods: {
    // 传值
    changeParams(){
      this.params.refer_line = this.refer_line
      this.$emit('changeParams',this.cur.i, this.params)
    },
    // 新增
    addItem(type){
      if(type == 1){
        if(!this.params.refer_line_switch) return
        this.refer_line.push({name:'',size:'',type:'',target:'',target_fixed:''})
      }else{
        if(!this.params.currency_conver_switch) return
        this.currency_conver.push({fields:'', currency_fields:''})
      }
    },
    // 删除
    deleteItem(type,index){
      if(type == 1){
        if(!this.params.refer_line_switch) return
        this.refer_line.splice(index, 1)
      }else{
        if(!this.params.currency_conver_switch) return
        this.currency_conver.splice(index, 1)
      }
    },
  },
  mounted() {
  },
}
</script>
<style lang="scss" scoped>
  @import '../../style.scss';
  .dashboard-style-other{
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
      margin-bottom: 10px;
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
    .card{
      line-height: 40px;
      p{
        display: flex;
        justify-content: flex-start;
        align-items: center;
      }
      &:last-child{
        margin-bottom: 0;
      }
      .name{
        display: inline-block;
        width: 74px;
      }
    }
    .card-cont{
      @include flex($jc: space-between, $alignI: center);
      .card{
        margin: 5px 0 15px;
        border: 1px solid $border;
        padding: 10px;
        box-shadow: 0 0 4px 2px #e9e9e9;
        border-radius: 4px;
        width: calc(100% - 55px);
      }
    }
    .btn{
      cursor: pointer;
      color: $main;
      font-size: 12px;
      &:hover{
       color: $main-hover;
      }
      &.dis{
       cursor: no-drop;
      }
    }
    ::v-deep .el-radio__label{
      font-size: 12px;
    }
    ::v-deep .el-input__inner{
      padding: 0 0 0 10px
    }
   }
  }
</style>
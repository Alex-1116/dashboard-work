<template>
  <div class="dashboard-target">
    <div class="card" :class="{'isHid':isHid}">
      <div v-show="!isHid" style="width:100%; height: 100%;">
        <p class="tit" v-show="!isHid">
          <span>选择报表数据来源类型</span>
          <span class="show" @click="showFunc">《 收起</span>
        </p>
        <div class="flex" style="margin-top:10px;">
          <el-select style="width:80%;" v-model="params.data_sheet" placeholder="请选择" size="mini" @change="selectOptions">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </div>
        <div class="flex" style="margin-top:10px;">
          <el-select style="width:80%;" v-model="params.data_sheet2" placeholder="请选择数据类型" size="mini" @change="selectOptions2">
            <el-option
              v-for="item in options2"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
          <span class="show">创建</span>
        </div>
        <div class="drag">
          <p class="tit" >选择字段<span class="show">刷新</span></p>
          <div class="search">
            <el-input v-model="search" @input="searchList" placeholder="搜索字段名称" size="mini" clearable />
            <Draggable tag="ul" handle=".item" v-model="params.all_fields" forceFallback="true"  group="list" class="list" @change="changeParams">
              <template v-if="params.all_fields.length == 0">
                <li class="noData" style="text-align: center;">暂无字段</li>
              </template>
              <template v-else>
                <li
                  class="item"
                  v-for="elem in params.all_fields"
                  :key="elem.id"
                >
                  <p v-if="elem.show">{{ elem.name }}<span class="tip">（{{ elem.tip }}）</span></p>
                </li>
              </template>
            </Draggable>
          </div>
        </div>
      </div>
      <div v-show="isHid" @click="showFunc" style="cursor: pointer;">
        <span class="show">展开 》</span>
      </div>
    </div>
    <div class="card">
      <p class="tit" >图表指标<span class="show">添加公式字段</span></p>
      <div class="list list2">
        <p style="margin:0 0 5px; display: flex;">
          <template v-if="['barChart','lineChart'].includes(cur.config.type)">横轴</template>
          <template v-else-if="['pieChart'].includes(cur.config.type)">分类指标</template>
          <template v-else>显示指标</template>
          （必填）
        </p>
        <Draggable tag="ul" handle=".info" v-model="params.x_fields" forceFallback="true" :group="params.x_fields.length > 0 ?'list1':'list'" @change="changeParams">
          <template v-if="params.x_fields.length == 0">
            <li class="noData">暂无字段</li>
          </template>
          <template v-else>
          <li
            class="item"
            v-for="elem in params.x_fields"
            :key="elem.id"
          >
            <span class="info">{{ elem.name }}<span class="tip">（{{ elem.tip }}）</span></span>
            <i class="icon el-icon-delete" @click="deleteItem(params.x_fields,elem,1)"></i>
            <i class="icon el-icon-edit-outline"></i>
          </li>
          </template>
        </Draggable>
        <p class="msg">最多一个字段</p>
        <template v-if="!['gaugeChart'].includes(cur.config.type)">
          <p style="margin:10px 0 5px; display: flex;">
            <template v-if="['barChart','lineChart'].includes(cur.config.type)">纵轴</template>
            <template v-else>数值指标</template>
            （必填）
          </p>
          <Draggable tag="ul" handle=".info" v-model="params.y_fields" forceFallback="true" :group="['barChart','lineChart'].includes(cur.config.type) ? 'list' : params.y_fields.length > 0 ? 'list2' : 'list' " style="height:calc(100% - 136px)" @change="changeParams">
            <template v-if="params.y_fields.length == 0">
              <li class="noData" style="text-align: center; padding-top:80px;">请将左侧字段拖至此处</li>
            </template>
            <template v-else>
            <li
              class="item"
              v-for="elem in params.y_fields"
              :key="elem.id"
            >
              <span class="info">{{ elem.name }}<span class="tip">（{{ elem.tip }}）</span></span>
              <i class="icon el-icon-delete" @click.prevent="deleteItem(params.y_fields,elem,2)"></i>
              <i class="icon el-icon-edit-outline"></i>
            </li>
            </template>
          </Draggable>
          <p class="msg">
            <template v-if="['barChart','lineChart'].includes(cur.config.type)">
              可多选
            </template>
            <template v-else>  
              最多一个字段
            </template>
          </p>
        </template>
      </div>
    </div>
  </div>
</template>
<script>
import Draggable from 'vuedraggable'
import _ from 'lodash'
export default {
  components: {
    Draggable
  },
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
        this.search = ''
      },
      immediate:true
      // deep: true
    },
  },
  data() {
    return { 
      isHid:false,
      options: [{
        value: '1',
        label: '单个表'
      }, {
        value: '2',
        label: '数据模型'
      }],
      options2: [{
        value: '1',
        label: '订单表'
      }, {
        value: '2',
        label: '库存排名表'
      }],
      search:'',
      params:{
        data_sheet: '',  //报表数据来源类型1
        data_sheet2: '', //报表数据来源类型2
        all_fields:[],   //所有字段
        x_fields:[],     //横轴/分类指标字段
        y_fields:[],     //纵轴/数值指标字段
      }
    }
  },
  computed:{
    // comp_type(){
    //   // 1 清空全部组件数据
    //   return this.$route.query.type == 1 ? 'clearAll': null
    // }
  },
  methods: {
    // 展开/收起
    showFunc(){
      this.isHid = !this.isHid
    },
    // 搜索
    searchList(val){
      this.params.all_fields.forEach(item=>{
        if(item.name.indexOf(val) > -1){
          item.show = true
        }else{
          item.show = false
        }
      })
    },
    // 删除
    deleteItem(list,item,type){
      const arr = _.filter(list,function(e){
        return e.id != item.id
      })
      this.params.all_fields.push(item)
      if(type == 1){
        this.params.x_fields = arr
      }else{
        this.params.y_fields = arr
      }
      this.changeParams()
    },
    // 选择数据报表来源类型
    selectOptions(){
      this.params.data_sheet2 = ''
      this.params.all_fields = []
      this.params.x_fields = []
      this.params.y_fields = []
      this.changeParams()
    },
    selectOptions2(){
      this.params.all_fields = [{name:'fba相关',tip:'fba_001',id:1, show:true},{name:'销量',tip:'字段英文名',id:2, show:true}, {name:'销售额',tip:'字段英文名',id:3, show:true}]
      this.params.x_fields = []
      this.params.y_fields = []
      this.changeParams()
    },
    // 传递参数
    changeParams(type){
      this.$emit('changeParams',this.cur.i, this.params, type)
    }
  },
};
</script>
<style lang="scss" scoped>
  @import '../../style.scss';
  .dashboard-target{
    display: flex;
    height: 100%;
    color: #666;
    .card{
      width: 100%;
      overflow: hidden;
      padding-right: 10px;
      &:first-child{
        border-right: 1px solid $border;
        margin-right: 10px;
      }
      &.isHid{
        width: 50px;
      }
      .tit{
        width: 100%;
        display: flex;
        justify-content: space-between;
        align-items: center;
      }
      .flex{
        display: flex;
        justify-content: space-between;
        align-items: center;
      }
      .show{
        color: $main;
        float: right;
        cursor: pointer;
        &:hover{
          color:$main-hover;
        }
      }
      .drag{
        margin-top: 10px;
        height: calc(100% - 103px);
        .search{
          margin-top: 10px;
          height: calc(100% - 27px);
        }
      }
      .list{
        border: 1px solid $border;
        border-top: none;
        cursor: pointer;
        width: 100%;
        height: calc(100% - 31px);
        li{
          border-radius: 4px;
          margin: 2px;
          padding: 0 6px;
          line-height: 25px;
          p{
            display: flex;
            justify-content: flex-start;
            align-items: center;
          }
          .info{
            display: inline-block;
            width: calc(100% - 30px);
          }
          .tip{
            color: #bebebe;
          }
          &:not(.noData):hover{
            color: #fff;
            background: $main;
            .tip{
             color: #fff;
            }
          }
        }
        .msg{
          background: #dcdcdc;
          color: #999;
          text-align: center;
          line-height: 26px;
        }
      }
      .list2{
        border-top: 1px solid $border;
        padding: 6px 4px;
        height: calc(100% - 22px);
        margin-top: 5px;
        box-sizing: border-box;
        ul{
          min-height: 30px;
          border: 1px solid $border;
          border-bottom: none;
          .icon{
            float: right;
            margin: 6px 0 0 2px;
          }
        }
      }
    }
    .card:last-child{
      padding-right: 0;
    }
  }
</style>
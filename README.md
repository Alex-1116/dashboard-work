### ReportForm

#### 安装教程

1.  数据大屏【低代码工作台】
2.  xxxx
3.  xxxx

#### 使用说明

1.  yarn run serve
2.  xxxx
3.  xxxx

#### 所使用到的第三方插件

1. elementUI        UI组件库   
2. echart           5.3.2  图表类库
3. vue-grid-layout  2.3.12 栅格布局系统  https://jbaysolutions.github.io/vue-grid-layout/zh/guide/
4. vuedraggable     2.20.0 拖拽库      https://www.jianshu.com/p/1b703a63af01
5. lodash           工具类库
6. resize-observer-polyfill  1.5.1 监听尺寸的变化 
7. scss             样式库
  
#### 目录结构
├── components          # 组件目录
│   │── Charts          # 图表组件
│   │── Header          # 顶部操作区 
│   │── ResizeObserver  # 自适应宽高组件 
│   │── SelectArea      # 图表展示区
│   │── SelectGroup     # 图表筛选区
│   │── WorkArea        # 图表筛选、搜索区
│   │── WorkOper        # 图表操作区
│   │   │── target       # 报表指标
│   │   │── style        # 页面样式 
│   │   │── other        # 其他   
│   │── Wrapper          # 组件包裹区域，含标题        
├── index.vue        # 入口文件                   
├── README.md        # 阅读文档                     
│── style.scss       # 公共样式表     

#### 注：
1. WorkArea 组件里的 defaultParams字段是所有组件参数的存储对象，通过监听里面字段的变化来传值给组件，所以一旦在组件里新增参数时也需要在defaultParams添加同样的参数。
2. defaultParams 里的字段可根据后端接口定义需重新配置。
3. 目前仅开发折线图、柱状图、饼图、仪表盘组件，Charts文件里通过 getChartData来请求接口获取相应的xAxis、series数据，  目前是模拟数据

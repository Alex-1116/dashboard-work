<script>
import ResizeObserver from 'resize-observer-polyfill'
import _ from 'lodash'
/**
 * @displayName ResizeObserver
 * @desc 监听子级元素的广告变化，告知上层或者下层组件。
 */
export default {
  data() {
    return {
      rect: {},
      activeStatus: true
    }
  },
  props: {
    /**
     * 是否开启debounce，0表示不开启。
     */
    debounce: {
      type: Number,
      default: 0
    },
    /**
     * 是否开启throttle，0表示不开启。 如果设置throttle 那么debounce的设置无效
     */
    throttle: {
      type: Number,
      default: 0
    },
    /**
     * 是否计算boundingRect
     */
    boundingRect: {
      type: Boolean,
      default: false
    }
  },
  created() {
    this.debounceChangeRect = _.debounce(this.changeRect, this.debounce)
    this.throttleChangeRect = _.throttle(this.changeRect, this.throttle)
  },
  mounted() {
    this.ro = new ResizeObserver((entries, observer) => {
      const entry = entries[0]
      if (!entry) {
        this.rect = {}
        return
      }

      let effect
      if (this.throttle) {
        effect = this.throttleChangeRect
      } else if (this.debounce) {
        effect = this.debounceChangeRect
      } else {
        effect = this.changeRect
      }
      effect(entry.contentRect.toJSON(), entry.borderBoxSize)
    })
    this.observe()
  },
  activated() {
    this.activeStatus = true
  },
  deactivated() {
    this.activeStatus = false
  },
  updated() {
    this.observe()
  },
  beforeDestroy() {
    this.ro && this.ro.unobserve(this.$el)
  },
  destroyed() {
    this.ro && this.ro.unobserve(this.$el)
  },
  methods: {
    observe() {
      const el = this.$el
      if (!el) {
        return
      }
      if (this.preEl == el) {
        return
      } else if (this.preEl) {
        this.ro.unobserve(this.preEl)
      }
      this.preEl = el
      this.ro.observe(el)
    },
    changeRect(val, borderBoxSize) {
      this.rect = val
      if (this.activeStatus) {
        let boundingRect
        if (this.boundingRect) {
          boundingRect = this.$el.getBoundingClientRect()
        }
        /**
         * @event resize
         * @property { ContentRect } 盒子的大小
         * @property { BorderBoxSize }
         * @property { BoundingRect }
         */
        this.$emit('resize', val, borderBoxSize, boundingRect)
      }
    }
  },
  render() {
    // const create = () =>
    return this.$slots.default
  }
}
</script>

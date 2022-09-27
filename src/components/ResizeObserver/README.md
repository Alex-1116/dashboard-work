### ResizeObserver

- 基本例子

```jsx static
import ResizeObserver from './index.vue'
export default {
  render() {
    return (
      <ResizeObserver
        onResize={() => {
          cosole.log('Resized !')
        }}
      >
        <div style="height:300px">
          <span>I Want Div Height </span>
        </div>
      </ResizeObserver>
    )
  }
}
```

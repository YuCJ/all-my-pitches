# How to Use D3 with React

---

## What is D3 actually?

D3 is a collections of modules handling different tasks. Tasks can be categorized into:

1. parse data, transform data structure
2. do math：包括座標轉換、資料內插、計算地圖投影等
3. manipulate dom

---

## 3 Patterns We Use D3 with React

1. Build a D3 concession in the React project
2. Use D3 to manipulate the virtual virtual DOM instead of real DOM
3. Use D3 as pure function modules math/graph

Maybe there is a 4... why we stick to React?

---

### Pattern 1: Build a D3 concession in the React project

- React 只負責 mount, unmount 劃出一塊租界，底下的 DOM tree update 都交給 D3 處理，React 不再過問（`shouldComponentUpdate() { return false; }）
- 缺點：不該再用 React 對這個 DOM tree 做什麼。

---

When should D3 take part in? `componentDidMount()` or  `ref` callbacks?

> React will call the `ref` callback with the DOM element when the component mounts, and call it with null when it unmounts. ref callbacks are invoked before `componentDidMount` or `componentDidUpdate` lifecycle hooks. -- [Callback Refs](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)

---

### Pattern 2: Use D3 to manipulate the fake virtual DOM

Use [Olical/react-faux-dom](https://github.com/Olical/react-faux-dom)

```javascript
import ReactFauxDOM from 'react-faux-dom'

class MyD3Chart extends React.Component {
  render () {
    // Create element.
    const fakeDOM = new ReactFauxDOM.createElement('div')

    // Change stuff using actual DOM functions.
    d3.select(fakeDOM)
        .data(this.props.data)
        .append('svg')

    // Render it to React elements.
    return fakeDOM.toReact()
  }
}

// Yields: <div style='color: red;' class='box'></div>
```



+++

範例：[React + D3.js: Balancing Performance & Developer Experience](https://medium.com/@tibotiber/react-d3-js-balancing-performance-developer-experience-4da35f912484)

缺點：

- 要研究如果用 D3 的 zoom, drag, transition 之類會動態更新 DOM 的功能時，React 怎麼去 trace 這些變化。例如用 [connectFauxDOM](https://github.com/Olical/react-faux-dom/blob/master/DOCUMENTATION.md#react-higher-order-component-hoc) 定時去追蹤 fakeDOM 的變化到更新 React element。
- 用 React 來 update data、操作 fakeDOM 轉成 react element、再更新 real DOM，真的有比 D3 直接操作 DOM 寫起來乾淨？效能更好嗎？

+++

優點：

- 可以用 React 的 `state`, `props` 來處理要給 D3 的資料
- 可以直接套用別人寫好的 D3 程式碼

---

### Pattern 3: Use D3 as pure function modules math/graph

優點：

- 效能應該是最好的、而且不用 import 整個 d3 library 只要取需要的部分

缺點：

- D3 處理資料的 code 直觀看不好理解
- 不能用 drag、transition、zoom 等功能

+++

Example: [d3-link](https://github.com/twreporter/static-fe-boilerplate/blob/master/soccer-ranking/src/components/result/compared-ranking/d3-link.js)

```javascript
import { linkHorizontal as d3Link } from 'd3-shape'
import PropTypes from 'prop-types'
import React, { PureComponent } from 'react'
import styled, { keyframes } from 'styled-components'

const Moving = keyframes`
  from {
    stroke-dashoffset: 2000;
  }
  to {
    stroke-dashoffset: 0;
  }
`

const Path = styled.path`
  stroke-dasharray: 2000;
  stroke-dashoffset: 0;
  animation: ${Moving} 2000ms ease-in 1200ms both;
  opacity: ${props => props.opacity};
  &:hover {
    opacity: 1;
  }
`

export default class D3Link extends PureComponent {
  static propTypes = {
    pathCoordinates: PropTypes.shape({
      start: PropTypes.shape({
        x: PropTypes.number.isRequired,
        y: PropTypes.number.isRequired,
      }).isRequired,
      end: PropTypes.shape({
        x: PropTypes.number.isRequired,
        y: PropTypes.number.isRequired,
      }).isRequired,
    }).isRequired,
  }

  render() {
    const { pathCoordinates, ...elseProps } = this.props
    const { start, end } = pathCoordinates
    const calculatePathD = d3Link()
      .x(d => d.x)
      .y(d => d.y)
    return (
      <Path
        d={calculatePathD({
          source: start,
          target: end,
        })}
        fill="none"
        {...elseProps}
      />
    )
  }
}
```

+++

Other Examples: 

- [How (and why) to use D3 with React](https://hackernoon.com/how-and-why-to-use-d3-with-react-d239eb1ea274)
- [source code of Recharts](https://github.com/recharts/recharts/tree/master/src/shape)
- [React + D3 繪製 svg 動態路線地圖     ](https://blog.arvinh.info/2017/07/21/d3-workshop-map/)



---

### Why we stick to React?

- 為什麼要同時使用兩套處理 data-view binding 的框架？我們用 React 是要處理什麼，是否真的有需求？

---

## Conclusion

- 如果要套用別人做好的整組 D3 code：Pattern 1 or Pattern 2
  - 如果有用到 D3 drag、transition、zoom 等功能：只能用 Pattern 1 讓 D3 來處理所有的事情，或是乾脆連 React 都不要用。代價是 data 的 store update 就無法透過 React 管理。（[強大的 d3 tarnsition](http://blog.infographics.tw/2016/11/d3js-transition/) ）
  - 如果沒有用到前述功能，就看你有沒有需要用到 React 來處理資料和這個 component 中的其他 element ，有就選 pattern 2。

+++

- 需要高度 customized 的功能：優先考慮 Pattern 3
  - 用 Pattern 3 自己處理 data -> view 的邏輯，D3 只是用來輔助的 helper
  - 如果需要處理大量 element 的動畫，或許用 pattern 1 結合 D3 的 canvas 來處理會比較好（Ref: [D3.js 實戰 － Canvas 把我的視覺化變「快」了！](http://blog.infographics.tw/2015/07/optimize-d3-with-canvas/)、[D3 + Canvas 繪製動態路線圖](https://blog.arvinh.info/2017/08/18/canvas-path-map/)）
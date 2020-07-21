<template>
  <div class="home">
    <div class="layoutJSON">
      Displayed as <code>[x, y, w, h]</code>:
      <div class="columns">
        <div class="layoutItem" v-for="item in layout" :key="item.i">
          <b>{{item.i}}</b>: [{{item.x}}, {{item.y}}, {{item.w}}, {{item.h}}]
        </div>
      </div>
    </div>
    <grid-layout
      :layout="layout"
      :col-num="parseInt(colNum)"
      :row-height="rowHeight"
      :maxRows="3"
      :is-draggable="draggable"
      :is-resizable="resizable"
      :is-mirrored="mirrored"
      :prevent-collision="preventCollision"
      :vertical-compact="true"
      :use-css-transforms="true"
      :responsive="responsive"
      @layout-created="layoutCreatedEvent"
      @layout-before-mount="layoutBeforeMountEvent"
      @layout-mounted="layoutMountedEvent"
      @layout-ready="layoutReadyEvent"
      @layout-updated="layoutUpdatedEvent"
    >
      <grid-item
        class="grid-item"
        v-for="item in layout"
        :key="item.i"
        :x="item.x"
        :y="item.y"
        :w="item.w"
        :h="item.h"
        :max-h="3"
        :i="item.i"
        @resize="resize"
        @move="move"
        @resized="resized"
        @container-resized="containerResized"
        @moved="moved"
      >
        <span>{{item.i}}</span>
      </grid-item>
    </grid-layout>
  </div>
</template>

<script>
import VueGridLayout from 'vue-grid-layout'
import _ from 'lodash'
const defaultLayout = [
  { x: 0, y: 0, w: 1, h: 1, i: '1' },
  { x: 1, y: 0, w: 1, h: 1, i: '2' },
  { x: 2, y: 0, w: 1, h: 1, i: '3' },
  { x: 3, y: 0, w: 1, h: 1, i: '4' },
  { x: 0, y: 1, w: 1, h: 1, i: '5' },
  { x: 1, y: 1, w: 1, h: 1, i: '6' },
  { x: 2, y: 1, w: 1, h: 1, i: '7' },
  { x: 3, y: 1, w: 1, h: 1, i: '8' },
  { x: 0, y: 2, w: 1, h: 1, i: '9' },
  { x: 1, y: 2, w: 1, h: 1, i: '10' },
  { x: 2, y: 2, w: 1, h: 1, i: '11' },
  { x: 3, y: 2, w: 1, h: 1, i: '12' }
]
export default {
  name: 'Home',
  components: {
    GridLayout: VueGridLayout.GridLayout,
    GridItem: VueGridLayout.GridItem
  },
  data () {
    return {
      defaultLayout: JSON.parse(JSON.stringify(defaultLayout)),
      layout: JSON.parse(JSON.stringify(defaultLayout)),
      draggable: true,
      resizable: true,
      mirrored: false,
      responsive: false,
      preventCollision: false,
      rowHeight: 280,
      colNum: 4,
      index: 0
    }
  },
  methods: {
    layoutCreatedEvent: function (newLayout) {
      // console.log('Created layout: ', newLayout)
    },
    layoutBeforeMountEvent: function (newLayout) {
      // console.log('beforeMount layout: ', newLayout)
    },
    layoutMountedEvent: function (newLayout) {
      // console.log('Mounted layout: ', newLayout)
    },
    layoutReadyEvent: function (newLayout) {
      // console.log('Ready layout: ', newLayout)
    },
    layoutUpdatedEvent: function (newLayout) {
      // console.log('Updated layout: ', newLayout)
    },
    /**
     * handleResize
     * @Describe 合并单元格
     * @param i 主键
     * @param newH w
     * @param newW h
     * @param newHPx hpx
     * @param newWPx wpx
     */
    resize: function (i, newH, newW, newHPx, newWPx) {
      console.log('RESIZE i=' + i + ', H=' + newH + ', W=' + newW + ', H(px)=' + newHPx + ', W(px)=' + newWPx)
      const origin = _.find(this.defaultLayout, { i: i })// 初始元素
      console.log(origin)
      // 单格合并
      if (origin.w < newW) {
        console.log(11)
        const out = _.find(this.defaultLayout, { x: (origin.x + (newW - 1)), y: origin.y })
        this.layout = _.without(this.layout, _.find(this.layout, { i: out.i }))
        // this.preOut.push(_.find(this.layout, { i: out.i }))
      }
      if (origin.h < newH) {
        const out = _.find(this.defaultLayout, { y: (origin.y + (newH - 1)), x: origin.x })
        this.layout = _.without(this.layout, _.find(this.layout, { i: out.i }))
      }
      // 多格合并
      const outs = []
      if (origin.w < newW && origin.h < newH) {
        _.each(this.layout, o => {
          if ((o.x <= origin.x + (newW - 1)) && (o.y <= origin.y + (newH - 1))) {
            outs.push(o)
          }
        })
        _.each(outs, e => {
          this.layout = _.without(this.layout, _.find(this.layout, { i: e.i }))
        })
        const newOrigin = {
          i: origin.i,
          x: origin.x,
          y: origin.y,
          w: newW,
          h: newH,
          moved: false
        }
        this.layout.push(newOrigin)
      }
    },
    resized: function (i, newH, newW, newHPx, newWPx) {
      // console.log('RESIZED i=' + i + ', H=' + newH + ', W=' + newW + ', H(px)=' + newHPx + ', W(px)=' + newWPx)
      let count = 0
      _.each(this.layout, e => {
        count = count + (e.w * e.h)
      })
      console.log(count)
      if (count < 12) {
      }
    },
    containerResized: function (i, newH, newW, newHPx, newWPx) {
      // console.log('CONTAINER RESIZED i=' + i + ', H=' + newH + ', W=' + newW + ', H(px)=' + newHPx + ', W(px)=' + newWPx)
    },
    move: function (i, newX, newY) {
      console.log('MOVE i=' + i + ', X=' + newX + ', Y=' + newY)
      const item = _.find(this.layout, { x: newX, y: newY })
      // console.log(item)
      const origin = _.find(this.layout, { i: i })
      // console.log(origin)
      _.each(this.layout, e => {
        if (e.i === item.i) {
          e.x = origin.x
          e.y = origin.y
        }
      })
      // console.log(item)
    },
    moved: function (i, newX, newY) {
      // console.log('### MOVED i=' + i + ', X=' + newX + ', Y=' + newY)
    }
  }
}
</script>
<style>
  .grid-item{
    background: #cccccc;
    border: 1px solid #000000;
  }
  .home{
    position: relative;
    width: 100%;
    height: 100%;
    overflow: hidden;
  }
  .layoutJSON{
    position: absolute;
    top: 0;
    left: 0;
    z-index: 2;
    background: rgba(0,0,0,0.6);
    color: #ffffff;
  }
</style>

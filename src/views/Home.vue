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
    <grid-layout v-if="showGrid"
      :layout.sync="layout"
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
      :autoSize="autoSize"
      :verticalCompact="verticalCompact"
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
      showGrid: true,
      defaultLayout: JSON.parse(JSON.stringify(defaultLayout)),
      layout: JSON.parse(JSON.stringify(defaultLayout)),
      draggable: true,
      resizable: true,
      mirrored: false,
      responsive: false,
      autoSize: false,
      verticalCompact: true,
      preventCollision: false,
      rowHeight: 280,
      colNum: 4,
      index: 0,
      lastOuts: []
    }
  },
  methods: {
    layoutCreatedEvent (newLayout) {
      // console.log('Created layout: ', newLayout)
    },
    layoutBeforeMountEvent (newLayout) {
      // console.log('beforeMount layout: ', newLayout)
    },
    layoutMountedEvent (newLayout) {
      // console.log('Mounted layout: ', newLayout)
    },
    layoutReadyEvent (newLayout) {
      // console.log('Ready layout: ', newLayout)
    },
    layoutUpdatedEvent (newLayout) {
      // console.log('Updated layout: ', newLayout)
      // this.showGrid = false
      // setTimeout(() => {
      //   console.log(newLayout)
      //   this.showGrid = true
      // }, 50)
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
    handleResize (i, newH, newW, newHPx, newWPx) {
      const origin = _.find(this.defaultLayout, { i: i })// 初始元素
      // 单格合并
      _.each(this.defaultLayout, o => {
        if ((o.x <= origin.x + (newW - 1)) && (o.y <= origin.y + (newH - 1))) {
          if (o.x >= origin.x && o.y >= origin.y) {
            if (o.i !== i) {
              const item = _.find(this.layout, { i: o.i })
              const index = _.findIndex(this.lastOuts, { i: o.i })
              if (index < 0) {
                this.lastOuts.push(o)
              }
              this.layout = _.without(this.layout, item)
            }
          }
        }
      })
      console.log(this.lastOuts)
    },
    autocomplete () {
      let count = 0
      _.each(this.layout, e => {
        count = count + (e.w * e.h)
      })
      console.log(count)
      if (count < 12) {
        let lCount = 0
        console.log(this.lastOuts)
        _.each(this.lastOuts, e => {
          if (e) {
            lCount = lCount + (e.w * e.h)
            if ((12 - count) >= lCount) {
              e.moved = false
              const item = _.find(this.defaultLayout, { i: e.i })
              if (item) {
                this.layout.push(item)
                this.lastOuts = _.without(this.lastOuts, e)
                // this.lastOuts = _.dropWhile(this.lastOuts, o => { return o.i === e.i })
              }
            }
            // this.lastOuts = _.dropWhile(this.lastOuts, o => { return o.i === e.i })
            // this.lastOuts = _.without(this.lastOuts, e)
          }
        })
        // this.defaultLayout = this.layout
      }
    },
    resize (i, newH, newW, newHPx, newWPx) {
      // console.log('RESIZE i=' + i + ', H=' + newH + ', W=' + newW + ', H(px)=' + newHPx + ', W(px)=' + newWPx)
      this.handleResize(i, newH, newW, newHPx, newWPx)
    },
    resized (i, newH, newW, newHPx, newWPx) {
      // console.log('RESIZED i=' + i + ', H=' + newH + ', W=' + newW + ', H(px)=' + newHPx + ', W(px)=' + newWPx)
      this.autocomplete()
    },
    containerResized (i, newH, newW, newHPx, newWPx) {
      // console.log('CONTAINER RESIZED i=' + i + ', H=' + newH + ', W=' + newW + ', H(px)=' + newHPx + ', W(px)=' + newWPx)
    },
    move (i, newX, newY) {
      console.log('MOVE i=' + i + ', X=' + newX + ', Y=' + newY)
      const item = _.find(this.layout, { x: newX, y: newY })
      const origin = _.find(this.defaultLayout, { i: i })
      _.each(this.layout, e => {
        if (e.i === i) {
          e.x = newX
          e.y = newY
        }
        if (e.i === item.i) {
          // 若之前有替换过位置的判断
          if (origin.x === newX && origin.y === newY) {
            // this.verticalCompact = false
            const newOrigin = _.find(this.defaultLayout, { i: item.i })
            e.x = newOrigin.x
            e.y = newOrigin.y
          } else {
            e.x = origin.x
            e.y = origin.y
          }
        }
      })
      // console.log(item)
    },
    moved (i, newX, newY) {
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

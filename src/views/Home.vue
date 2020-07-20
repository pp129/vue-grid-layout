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
      :layout.sync="layout"
      :col-num="parseInt(colNum)"
      :row-height="rowHeight"
      :max-rows="3"
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
        :static="item.static"
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
const testLayout = [
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
      layout: JSON.parse(JSON.stringify(testLayout)),
      layout2: JSON.parse(JSON.stringify(testLayout)),
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
      console.log('Created layout: ', newLayout)
    },
    layoutBeforeMountEvent: function (newLayout) {
      console.log('beforeMount layout: ', newLayout)
    },
    layoutMountedEvent: function (newLayout) {
      console.log('Mounted layout: ', newLayout)
    },
    layoutReadyEvent: function (newLayout) {
      console.log('Ready layout: ', newLayout)
    },
    layoutUpdatedEvent: function (newLayout) {
      console.log('Updated layout: ', newLayout)
    },
    resize: function (i, newH, newW, newHPx, newWPx) {
      console.log(
        'RESIZE i=' +
          i +
          ', H=' +
          newH +
          ', W=' +
          newW +
          ', H(px)=' +
          newHPx +
          ', W(px)=' +
          newWPx
      )
    },
    resized: function (i, newH, newW, newHPx, newWPx) {
      console.log(
        '### RESIZED i=' +
          i +
          ', H=' +
          newH +
          ', W=' +
          newW +
          ', H(px)=' +
          newHPx +
          ', W(px)=' +
          newWPx
      )
    },
    containerResized: function (i, newH, newW, newHPx, newWPx) {
      console.log(
        '### CONTAINER RESIZED i=' +
          i +
          ', H=' +
          newH +
          ', W=' +
          newW +
          ', H(px)=' +
          newHPx +
          ', W(px)=' +
          newWPx
      )
    },
    move: function (i, newX, newY) {
      console.log('MOVE i=' + i + ', X=' + newX + ', Y=' + newY)
    },
    moved: function (i, newX, newY) {
      console.log('### MOVED i=' + i + ', X=' + newX + ', Y=' + newY)
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
  }
</style>

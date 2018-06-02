<template>
  <div id="container" class="wrap">
      <button id="add" @click=newBtn()>增加节点</button>
      <div class="icon-logo"></div>
  </div>
</template>

<script>
import '../../assets/css/sprites-generated.css'
/*eslint-disable */
export default {
  name: 'jsPlumb',
  data () {
    return {
      instance: {},
      i: 0,
      pstyle: {
        Endpoint: ['Dot', { radius: 2 }],
        paintStyle: {
          strokeStyle:  '#2f8e00',
          fillStyle: 'none'
        },
        connector: ['Flowchart', {stub: [0, 0], gap: 2, cornerRadius: 5, alwaysRespectStubs: true }],
        connectorStyle: {
          lineWidth: 1,
          strokeStyle:  '#2f8e00'
        },
        maxConnections: -1
      }
    }
  },
  mounted () {
    this.initJsPlumb()
  },
  methods: {
    newBtn:function(){
      let container=$('#container')
      var $item = $('<div class="item" style="" id="item' + this.i + '"><span class="text">新增节点</span><span class="dragPoint"></span></div>')
      container.append($item)
      var id = $item.attr('id')
      this.renderConnect(id)
      this.i++
    },
    initJsPlumb: function () {
      var _this=this
      jsPlumb.bind("ready",function(){
        _this.instance = jsPlumb.getInstance({
        Endpoint: ['Dot', {radius: 2}],
        HoverPaintStyle: { strokeStyle: '#1e8151', lineWidth: 2 },
        ConnectionOverlays: [
          [ 'Arrow', {location: 1, id: 'arrow', length: 10, foldback: 0.8, width: 10} ],
          [ 'Label', {abel: '', id: 'label', cssClass: 'labelstyle' }]
        ],
        DragOptions: { zIndex: 2000 },
        Container: 'topocontent'
      })
      /* 连线后触发 */
      _this.instance.bind('connection', function (info) {
        var labelText = '同意'
        info.connection.id = '0001'
        info.connection.setLabel(labelText)
        console.log(info)
        if (info.sourceId == info.targetId) {
          alert('不能让以自己为目标元素')
          _this.instance.detach(info)
        }
      })
      })
    },
    /* 设置div可连线 */
    renderConnect:function (newid) { // 渲染
      this.instance.draggable(newid)
      var _this=this
      this.instance.doWhileSuspended(function () {
        var isFilterSupported = _this.instance.isDragFilterSupported()
        if (isFilterSupported) {
          _this.instance.makeSource(newid, {filter: '.dragPoint', anchor: 'Continuous'}, _this.pstyle)
        } else {
          var eps = jsPlumb.getSelector('.dragPoint')
          for (var i = 0; i < eps.length; i++) {
            var e = eps[i], p = e.parentNode
            _this.instance.makeSource(e, {parent: p, anchor: 'Continuous'}, _this.pstyle)
          }
        }
      })
      _this.instance.makeTarget(newid, {dropOptions: {hoverClass: 'dragHover'}, anchor: 'Continuous'}, _this.pstyle)
  }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  #container {
      height: 100%;
  }

  #container {
      position: relative;
  }

  #container .item {
      position: absolute;
      border: 1px solid #000;
      padding: 10px;
      padding-right: 25px;
      border-radius: 5px;
      cursor: move;
  }

  #container .item .dragPoint {
      display: inline-block;
      width: 20px;
      height: 20px;
      border: 1px solid #000;
      border-radius: 3px;
      position: absolute;
      top: 50%;
      transform: translate(0, -50%);
      cursor: pointer;
  }
  /*label样式*/

  #container ._jsPlumb_overlay {
      padding: 6px;
      font-size: 14px;
      background: #fff;
      border: 1px solid #00bb00;
      border-radius: 3px;
  }

  #container svg path {
      stroke-width: 2;
      cursor: pointer;
  }
  /*鼠标移入后前后变色*/

  #container ._jsPlumb_target_hover {
      background: #00a3ff;
  }

  #container ._jsPlumb_source_hover {
      background: #00a3ff;
  }
</style>

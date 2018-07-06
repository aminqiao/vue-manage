<template>
  <div id="container" class="wrap">
    <div id="left">
      <div class="node radius" id="node1">数据集一</div>
      <div class="node radius" id="node2">数据集二</div>
      <div class="node radius" id="node3">数据集三</div>
      <div class="node radius" id="node4">数据集四</div>
    </div>
    <div
      id="right"
      @mousedown.self="handleMouseDown"
      @mousemove.self="handleMouseMove"
      @mouseup="handleMouseUp"
    >
      <p>拖拉到此区域</p>
    </div>
    <div id="save">
      <input type="button" value="保存" onclick="save()" />
    </div>
  </div>
</template>
<script>
import jsPlumb from 'jsplumb'
import jqueryui from 'jqueryui'
import $ from 'jquery'
/*eslint-disable */
export default {
  data () {
    return {
      instance: {},
      i: 0,
      connectorPaintStyle: {
        lineWidth: 4,
        strokeStyle: "#61B7CF",
        joinstyle: "round",
        outlineColor: "white",
        outlineWidth: 2
      },
      connectorHoverStyle: {
        lineWidth: 4,
        strokeStyle: "#216477",
        outlineWidth: 2,
        outlineColor: "white"
      },
      hollowCircle: {
        endpoint: ["Dot", { radius: 2 }],  //端点的形状
        connectorStyle: this.connectorPaintStyle,//连接线的颜色，大小样式
        connectorHoverStyle: this.connectorHoverStyle,
        paintStyle: {
          stroke: "#1e8151",
          fill: "transparent",
          radius: 5,
          lineWidth: 2
        },//端点的颜色样式
        //anchor: "AutoDefault",
        isSource: true,    //是否可以拖动（作为连线起点）
        connector: ["Flowchart", { stub: [40, 60], gap: 10, cornerRadius: 5, alwaysRespectStubs: true }],  //连接线的样式种类有[Bezier],[Flowchart],[StateMachine ],[Straight ]
        isTarget: true,    //是否可以放置（连线终点）
        maxConnections: -1,    // 设置连接点最多可以连接几条线
        connectorOverlays: [["Arrow", { width: 10, length: 10, location: 1 }]]
      },
      selList: [],
      _x: null,
      _y: null,
      selDiv: null,
      startX: null,
      startY: null,
      evt: null,
      isSelect: null
    }
  },
  mounted () {
    var _this = this
    _this.initJsPlumb()
    $("#left").children().draggable({
        helper: "clone",
        scope: "ss",
    })
    $("#right").droppable({
        scope: "ss",
        drop: function (event, ui) {
            var left = parseInt(ui.offset.left - $(this).offset().left);
            var top = parseInt(ui.offset.top - $(this).offset().top);
            var name = ui.draggable[0].id;
            _this.i = _this.i+1;
            var id = "state_start" + _this.i;
            $(this).append('<div class="node" style="border-radius: 25em"  id="' + id + '" >' + $(ui.helper).html() + '</div>');
            $("#" + id).css("left", left).css("top", top);
            _this.renderConnect(id)
        }
    })
  },
  methods: {
    initJsPlumb: function () {
      var _this=this
      jsPlumb.jsPlumb.bind("ready",function(){
        _this.instance = jsPlumb.jsPlumb.getInstance({
        Endpoint: ['Dot', {radius: 1}],
        ConnectionOverlays: [
          [ 'Arrow', {location: 1, id: 'arrow', length: 10, foldback: 0.8, width: 10} ]
        ],
        DragOptions: { zIndex: 2000 },
        Container: 'topocontent'
      })
      /* 连线后触发 */
      _this.instance.bind('connection', function (info) {
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
      console.error(jsPlumb.jsPlumb)
      this.instance.addEndpoint(newid, { anchors: "TopCenter" }, this.hollowCircle);
/*       this.instance.addEndpoint(newid, { anchors: "RightMiddle" }, this.hollowCircle);
      this.instance.addEndpoint(newid, { anchors: "BottomCenter" }, this.hollowCircle);
      this.instance.addEndpoint(newid, { anchors: "LeftMiddle" }, this.hollowCircle); */
      this.instance.draggable(newid);
      $("#" + newid).draggable({ containment: "parent" });
    },
    handleMouseDown: function () {
      console.log("handleMouseDown")
      var fileNodes = $("#right").find(".node")
      for ( var i = 0; i < fileNodes.length; i++) { 
        this.selList.push(fileNodes[i])
      } 
      this.isSelect = true; 
      var evt = window.event || arguments[0]; 
      this.startX = (evt.x || evt.clientX) - 200; 
      this.startY = (evt.y || evt.clientY); 
      var selDiv = document.createElement("div"); 
      selDiv.style.cssText = "position:absolute;width:0px;height:0px;font-size:0px;margin:0px;padding:0px;border:1px dashed #0099FF;background-color:#C3D5ED;z-index:1000;filter:alpha(opacity:60);opacity:0.6;display:none;"; 
      selDiv.id = "selectDiv"; 
      document.getElementById("right").appendChild(selDiv);
    
      selDiv.style.left = this.startX -200 + "px";
      selDiv.style.top = this.startY + "px"; 
      this.selDiv = document.getElementById("selectDiv")
    },
    handleMouseMove: function () {
      console.log("handleMouseMove")
      var evt = window.event || arguments[0]; 
      if (this.isSelect) { 
        if (this.selDiv.style.display == "none") { 
          this.selDiv.style.display = ""; 
        } 
        this._x = (evt.x || evt.clientX) - 200; 
        this._y = (evt.y || evt.clientY); 
        this.selDiv.style.left = Math.min(this._x, this.startX) + "px"
        this.selDiv.style.top = Math.min(this._y, this.startY) + "px"
        this.selDiv.style.width = Math.abs(this._x - this.startX) + "px"
        this.selDiv.style.height = Math.abs(this._y - this.startY) + "px"
  
        // ---------------- 关键算法 ---------------------  
        var _l = this.selDiv.offsetLeft, _t = this.selDiv.offsetTop; 
        var _w = this.selDiv.offsetWidth, _h = this.selDiv.offsetHeight; 
        for ( var i = 0; i < this.selList.length; i++) { 
          var sl = this.selList[i].offsetWidth + this.selList[i].offsetLeft; 
          var st = this.selList[i].offsetHeight + this.selList[i].offsetTop; 
          
          if (sl > _l && st > _t && this.selList[i].offsetLeft < _l + _w && this.selList[i].offsetTop < _t + _h) { 
            if (this.selList[i].className.indexOf("seled") == -1) { 
              this.selList[i].className = this.selList[i].className + " seled"; 
            } 
          } else { 
            if (this.selList[i].className.indexOf("seled") != -1) { 
              this.selList[i].className = "fileDiv"; 
            } 
          } 
        }
      } 
    },
    handleMouseUp: function () {
      this.isSelect = false; 
      console.log("handleMouseUp")
      if (this.selDiv) { 
        document.getElementById("right").removeChild(this.selDiv); 
        var sourceID = this.showSelDiv(this.selList); 
        var id = this.i +1
        $("#right").append('<div class="node" style="border-radius: 25em;left:100px;top:200px"  id="state_start' + id + '" >结果</div>');
        this.renderConnect("state_start"+id)
        this.line(sourceID,"state_start"+id)
      } 
      this.selList = []
      this._x = null
      this._y = null
      this.selDiv = null
      this.startX = null
      this.startY = null
    },
    showSelDiv: function(arr) { 
      var sourceId = [];
      for ( var i = 0; i < arr.length; i++) { 
        if (arr[i].className.indexOf("seled") != -1) { 
          sourceId.push(arr[i].getAttribute('id'))
        } 
      } 
      return sourceId
    },
    line: function (sourceId, targetId) {
      let common={
        overlays:[
          ["Custom", {
            create:function(component) {
                return $("<select id='myDropDown'><option value='foo'>foo</option><option value='bar'>bar</option></select>");                
              },
              location:1,
              id:"customOverlay"
            }]
         ]
      }
      if (sourceId.length>1){
        for (let i in sourceId) {
          this.instance.connect({ source:sourceId[i], target:targetId }, this.hollowCircle);
        }
      }
      
      
    } 
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.node {
    box-shadow: 2px 2px 19px #aaa;
    -o-box-shadow: 2px 2px 19px #aaa;
    -webkit-box-shadow: 2px 2px 19px #aaa;
    -moz-box-shadow: 2px 2px 19px #aaa;
    -moz-border-radius: 0.5em;
    border-radius: 0.5em;
    opacity: 0.8;
    filter: alpha(opacity=80);
    border: 1px solid #346789;
    width: 50px;
    /*line-height: 40px;*/
    text-align: center;
    z-index: 20;
    position: absolute;
    background-color: #eeeeef;
    color: black;
    padding: 10px;
    font-size: 9pt;
    cursor: pointer;
    height: 20px;
    line-height: 20px;
}
.radius {
    border-radius: 25em;
}
.node:hover {
    box-shadow: 2px 2px 19px #444;
    -o-box-shadow: 2px 2px 19px #444;
    -webkit-box-shadow: 2px 2px 19px #444;
    -moz-box-shadow: 2px 2px 19px #444;
    opacity: 0.8;
    filter: alpha(opacity=80);
}
		.dragActive {
			border: 2px dotted orange;
		}

		.dropHover {
			border: 1px dotted red;
		}

		.item {
			border: 1px solid black;
			background-color: #ddddff;
			width: 100px;
			height: 100px;
			position: absolute;
		}

		#node1 {
			left: 55px;
			top: 100px;
		}

		#node2 {
			left: 55px;
			top: 200px;
		}

		#node3 {
			left: 55px;
			top: 300px;
		}

		#node4 {
			left: 55px;
			top: 400px;
		}

		#left {
			border: 1px solid #00bfff;
			width: 200px;
			height: 750px;
			position: absolute;
		}

		#right {
			border: 1px solid #00bfff;
			width: 1024px;
			position: absolute;
			height: 750px;
			left: 255px;
		}

		#save {
			border: 1px solid #00bfff;
			width: 150px;
			height: 750px;
			position: absolute;
			left: 1250px;
		}
</style>

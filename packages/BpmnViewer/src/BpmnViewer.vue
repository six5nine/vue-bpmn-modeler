<template>
  <div class="containers">
    <div ref="canvas" class="canvas"></div>
    <!-- <div ref="canvas1" class="canvas"></div> -->
  </div>
</template>

<script>
import BpmnViewer from 'bpmn-js/lib/NavigatedViewer';
export default {
  name: "BpmnViewer",
  props: {
    xmlData: {
      type: String,
      default: ''
    },
    taskData: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      taskList: [],
	  bpmnViewer: {},
    };
  },
   watch: {
    xmlData(val) {
      this.renderBpmnView()
    }
  },
  mounted() {
    this.taskList = this.taskData
    this.bpmnViewer = new BpmnViewer({
      container: this.$refs["canvas"],
      bpmnRenderer: {
          // defaultFillColor: "rgba(30,30,30,1)",
          // defaultStrokeColor: "red"
      }
    });
    // this.bpmnViewer.on('import.parse.start', (re) => {
    //   console.log(11111, re)
    // })
    // console.log('bpmnViewer', this.bpmnViewer)
	this.renderBpmnView();
  },
  methods: {
	renderOverlays() {
		const overlays = this.bpmnViewer.get('overlays');
			let overlayHtml = document.createElement('div');
			overlayHtml.classList.add('node-statistic');
			//overlayHtml.style.width = '24px';
			//overlayHtml.style.height = '24px';
			overlayHtml.innerHTML = '<svg width="24" height="24" viewBox="0 0 24 24"><g fill="none" fill-rule="evenodd">'+
				'<circle cx="12" cy="12" r="12" fill="#10D070"></circle>'+
				'<path fill="#FFF" d="M12,19 C8.13400675,19 5,15.8659932 5,12 C5,8.13400675 8.13400675,5 12,5 C15.8659932,5 19,8.13400675 19,12 C19,15.8659932 15.8659932,19 12,19 Z M12,17 C14.7614237,17 17,14.7614237 17,12 C17,9.23857625 14.7614237,7 12,7 C9.23857625,7 7,9.23857625 7,12 C7,14.7614237 9.23857625,17 12,17 Z M12,16 C9.790861,16 8,14.209139 8,12 C8,9.790861 9.790861,8 12,8 C14.209139,8 16,9.790861 16,12 C16,14.209139 14.209139,16 12,16 Z"></path>'+
				'</g></svg><span style="box-sizing: border-box;padding: 0px 11px 0px 0px;width:24px">2</span>';

			overlays.add('usertask1', {
				position: {bottom: 12, left: 0},
				html: overlayHtml
			});
			console.log('this.bpmnViewer overlays', overlays)
	},
	async renderBpmnView() {
	    try {
		  // let xmlData = this.xmlData.replace('xmlns:camunda', 'xmlns:bioc="http://bpmn.io/schema/bpmn/biocolor/1.0" xmlns:camunda')
		  // console.log('xmlData', xmlData)
		  const { warning } = await this.bpmnViewer.importXML(this.xmlData);
		  // console.log('rendered', warning);
		  let canvas = this.bpmnViewer.get('canvas');
		  this.bpmnViewer.getDefinitions().rootElements[0].flowElements.forEach(n => {
			canvas.addMarker(n.id, 'op-selectable');
			
			}
		  );
		  this.renderOverlays();
		  // canvas.zoom('fit-viewport')
		  if (this.taskList && this.taskList.length > 0) {
			// let overlays = this.bpmnViewer.get('overlays');
			// let overlayHtml = document.createElement('div');
			// overlays.add('StartEvent_1', {
			//   position: {
			//     top: 0,
			//     right: 0,
			//   },
			//   html: overlayHtml
			// });
			// 判断开始节点或结束节点完成
			// const modeling = this.bpmnViewer.get('modeling')
			// modeling.setColor({ fill: 'red', stroke: 'red' })
			// console.log('this.bpmnViewer', this.bpmnViewer)
			// console.log('canvas', canvas)
			// this.bpmnViewer.getDefinitions().$attrs['xmlns:bioc'] = 'http://bpmn.io/schema/bpmn/biocolor/1.0'
			// this.bpmnViewer.getDefinitions().rootElements[0].di.planeElement.forEach(n => {
			//   // console.log(n)
			//   n['bioc:stroke'] = 'red'
			//   n['bioc:fill'] = "#E1BEE7"
			//   // console.log(n)
			// })
			// this.bpmnViewer.getDefinitions().diagrams[0].plane.planeElement.forEach(n => {
			//   // console.log(n)
			//   n['bioc:stroke'] = 'red'
			//   n['bioc:fill'] = "#E1BEE7"
			//   n.di = {'bioc:stroke': 'red'}
			//   // console.log(n)
			// })
			// console.log('bpmnViewer2', this.bpmnViewer.getDefinitions())
			// const result = await this.bpmnViewer._moddle.toXML(this.bpmnViewer.getDefinitions(), {});
			// console.log(result)
			// result.xml = result.xml.replace(/\n/g, '');
			// result.xml = result.xml.replace('xmlns:camunda', 'xmlns:bioc="http://bpmn.io/schema/bpmn/biocolor/1.0" xmlns:camunda')
			// result.xml = result.xml.replace('bpmnElement="EndEvent_1xgys24"', 'bpmnElement="EndEvent_1xgys24" bioc:stroke="#3399aa"')
			// let bpmnViewer1 = new BpmnViewer({
			//   container: this.$refs["canvas1"],
			//   bpmnRenderer: {
			//       // defaultFillColor: "rgba(30,30,30,1)",
			//       // defaultStrokeColor: "red"
			//   }
			// });
			// const { warning } = await bpmnViewer1.importXML(result.xml);
			// console.log('this.bpmnViewer', bpmnViewer1)
			
			this.bpmnViewer.getDefinitions().rootElements[0].flowElements.forEach(n => {

			  if (n.$type === 'bpmn:UserTask') {
				  let completeTask = this.taskList.find(m => m.key === n.id)
				  let todoTask = this.taskList.find(m => !m.completed)
				  let endTask = this.taskList[this.taskList.length - 1]
				  if (completeTask) {
					canvas.addMarker(n.id, completeTask.completed ? 'highlight' : 'highlight-todo');  
					n.outgoing.forEach(nn => {
					  let targetTask = this.taskList.find(m => m.key === nn.targetRef.id)
					  if (targetTask) {
						canvas.addMarker(nn.id, targetTask.completed ? 'highlight' : 'highlight-todo');  
					  } else if (nn.targetRef.$type === 'bpmn:ExclusiveGateway') {
						// canvas.addMarker(nn.id, 'highlight');
						canvas.addMarker(nn.id, completeTask.completed ? 'highlight' : 'highlight-todo');  
						canvas.addMarker(nn.targetRef.id, completeTask.completed ? 'highlight' : 'highlight-todo');  
					  } else if (nn.targetRef.$type === 'bpmn:EndEvent') {
						if (!todoTask && endTask.key === n.id) {
						  canvas.addMarker(nn.id, 'highlight');  
						  canvas.addMarker(nn.targetRef.id, 'highlight');  
						}
						if (!completeTask.completed) {
						  canvas.addMarker(nn.id, 'highlight-todo');  
						  canvas.addMarker(nn.targetRef.id, 'highlight-todo');  
						}
					  }
					});
				  }
			  } else if (n.$type === 'bpmn:ExclusiveGateway') {
				n.outgoing.forEach(nn => {
				  let targetTask = this.taskList.find(m => m.key === nn.targetRef.id)
				  if (targetTask) {
					canvas.addMarker(nn.id, targetTask.completed ? 'highlight' : 'highlight-todo');
				  }
				})
			  }
			  if (n.$type === 'bpmn:StartEvent') {
				n.outgoing.forEach(nn => {
				  let completeTask = this.taskList.find(m => m.key === nn.targetRef.id)
				  if (completeTask) {
					canvas.addMarker(nn.id, 'highlight');
					canvas.addMarker(n.id, 'highlight');
					return
				  }
				});
			  }
			  
			})
		  }
		} catch (err) {
		  console.log('error rendering', err);
		}
		
	}
  }
};
</script>
<style lang="less">
.node-statistic {
    display: flex;
    line-height: 24px;
    height: 24px;
    font-family: IBMPlexSans;
    font-size: 13px;
    font-weight: bold;
    transform: translateX(-50%);
    background-color: rgb(16, 208, 112);
    color: rgb(255, 255, 255);
    border-radius: 12px;
}
.djs-element.hover .djs-outline, .djs-element.selected .djs-outline {
    stroke-width: 0px;
}

.djs-shape.op-selectable.hover .djs-outline {
    stroke-width: 3px!important;
    stroke: rgb(77, 144, 255)!important;
	fill: rgba(189, 212, 253, 0.5) !important;
	stroke-dasharray: 3,0;
	rx: 14px;
  ry: 14px;
	cursor: pointer;
}
.djs-shape.selected .djs-outline {
    stroke-width: 3px;
    stroke: rgb(77, 144, 255);
    fill: rgba(189, 212, 253, 0.5) !important;
    stroke-dasharray: 3,0;
    	rx: 14px;
  ry: 14px;
	cursor: pointer;
}
.containers {
  position: absolute;
  background-color: #ffffff;
  top:0;
  left: 0;
  width: 100%;
  height: 100%;
}
.canvas {
  width: 100%;
  height: 100%;
}
/deep/.highlight.djs-shape .djs-visual > :nth-child(1) {
  fill: green !important; 
  stroke: green !important;
  fill-opacity: 0.2 !important;
}
/deep/.highlight.djs-shape .djs-visual > :nth-child(2) {
  fill: green !important;
}
/deep/.highlight.djs-shape .djs-visual > path {
  fill: green !important;
  fill-opacity: 0.2 !important;
  stroke: green !important;
}
/deep/.highlight.djs-connection > .djs-visual > path {
  stroke: green !important;
}
/deep/.highlight-todo.djs-connection > .djs-visual > path {
  stroke: orange !important;
  stroke-dasharray: 4px !important;
  fill-opacity: 0.2 !important;
  marker-end: url(#sequenceflow-end-_E7DFDF-_E7DFDF-803g1kf6zwzmcig1y2ulm5egr);
}
/deep/.highlight-todo.djs-shape .djs-visual > :nth-child(1) {
  fill: orange !important; 
  stroke: orange !important;
  stroke-dasharray: 4px !important; 
  fill-opacity: 0.2 !important;
}
/deep/.overlays-div {
  font-size: 10px;
  color: red;
  width: 100px;
  top: -20px !important;
}
</style>
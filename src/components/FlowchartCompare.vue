<template>
  <div>
    <div>
      <h1>Flowchart</h1>
      <div v-if="!flowchartData.readOnly">
        <h4>Flowchart will reset on refresh</h4>
        <button type="button" @click="$refs.chart.add(getNewNode({ x:10, y:10 }))">
            Add Node (Double click on Canvas)
        </button>
        <button type="button" @click="$refs.chart.remove()">
            Delete Node (Select Node/Connection and click Delete)
        </button>
        <button type="button" @click="$refs.chart.editCurrent()">
            Edit (Double Click on Node/Connection for options)
        </button>
        <button type="button" @click="$refs.chart.save()">
            Save
        </button>
      </div>
      <h4 v-if="flowchartData.readOnly">Flowchart Saved, You can no longer Edit, refresh to reset flowchart</h4>
      <flow-chart
        style="margin: auto;"
        :nodes="flowchartData.nodes"
        :connections="flowchartData.connections"
        @editnode="handleEditNode"
        @editconnection="handleEditConnection"
        @save="handleChartSave"
        @dblclick="$refs.chart.add(getNewNode($event))"
        :readonly="flowchartData.readOnly"
        ref="chart">
      </flow-chart>
      <node-dialog
        :visible.sync="flowchartData.nodeDialogVisible"
        :node.sync="flowchartData.nodeForm.target">
      </node-dialog>
      <connection-dialog
        :visible.sync="flowchartData.connectionDialogVisible"
        :connection.sync="flowchartData.connectionForm.target"
        :operation="flowchartData.connectionForm.operation">
      </connection-dialog>
    </div>
    <br><hr><br>
    <h1>Code Diff (Text)</h1>
    <h4>Data will reset on refresh</h4>
    <br>
    Type of Output:
    <select name="type" id="type" v-model="codeDiffData.type">
      <option value="side-by-side" default>Side by Side</option>
      <option value="line-by-line">Line by Line</option>
    </select>
    <br><br>
    Old Text:
    <textarea name="old" id="old" cols="30" rows="5" v-model="codeDiffData.oldStr"></textarea>
    New Text:
    <textarea name="new" id="new" cols="30" rows="5" v-model="codeDiffData.newStr"></textarea>
    <code-diff :old-string="codeDiffData.oldStr" :new-string="codeDiffData.newStr" :outputFormat="codeDiffData.type" :context="10" />
    <br><hr><br>
    <h1>JSON Compare (JSON)</h1>
    <h4>Data will reset on refresh</h4>
    <br>
    Old JSON:
    <textarea name="old" id="old" cols="30" rows="5" v-model="jsonCompareData.oldDataStr"></textarea>
    New JSON:
    <textarea name="new" id="new" cols="30" rows="5" v-model="jsonCompareData.newDataStr"></textarea>
    <h4>Result won't update until JSON is in valid format</h4>
    <vue-json-compare
      :oldData="jsonCompareData.oldData"
      :newData="jsonCompareData.newData">
    </vue-json-compare>
  </div>
</template>

<script>
import CodeDiff from 'vue-code-diff'
import vueJsonCompare from 'vue-json-compare'
import FlowChart from 'flowchart-vue';
import NodeDialog from './NodeDialog'
import ConnectionDialog from './ConnectionDialog'

export default {
  name: 'FlowchartCompare',
  components: {
    CodeDiff,
    vueJsonCompare,
    FlowChart,
    NodeDialog,
    ConnectionDialog
  },
  data(){
    return {
      codeDiffData: {
        oldStr: 'old code\nnew code',
        newStr: 'new code\nold code',
        type: 'side-by-side',
      },
      jsonCompareData: {
        oldDataStr: JSON.stringify({a: "Test",e: 0}),
        newDataStr: JSON.stringify({a: [2,4],b: 3}),
        oldData: {a: "Test",e: 0},
        newData: {a: [2,4],b: 3}
      },
      flowchartData: {
        nodes: [
          {id: 1, x: 140, y: 270, name: 'Start', type: 'start'},
          {id: 2, x: 540, y: 270, name: 'End', type: 'end'},
        ],
        connections: [
          {
            source: {id: 1, position: 'right'},
            destination: {id: 2, position: 'left'},
            id: 1,
            type: 'pass',
          },
        ],
        nodeForm: { target: null },
        connectionForm: { target: null, operation: null },
        nodeDialogVisible: false,
        connectionDialogVisible: false,
        readOnly: false
      },
    }
  },
  methods: {
    getNewNode(position) {
      let nodeID = +new Date();
      let node = { id: nodeID, x: position.x, y: position.y, name: "New Node", type: "operation" };
      return node
    },
    handleChartSave(nodes, connections) {
      console.log("Save: ",nodes,connections)
      this.flowchartData.readOnly = true;
    },
    handleEditNode(node) {
      this.flowchartData.nodeForm.target = node;
      this.flowchartData.nodeDialogVisible = true;
    },
    handleEditConnection(connection) {
      this.flowchartData.connectionForm.target = connection;
      this.flowchartData.connectionDialogVisible = true;
    }
  },
  watch: {
    'jsonCompareData.oldDataStr': function() {
      let json = null;
      try {
        json = JSON.parse(this.jsonCompareData.oldDataStr);
        this.jsonCompareData.oldData = json;
      } catch (error) {
        json = {}
      }
    },
    'jsonCompareData.newDataStr': function() {
      let json = null;
      try {
        json = JSON.parse(this.jsonCompareData.newDataStr);
        this.jsonCompareData.newData = json;
      } catch (error) {
        json = {}
      }
    }
  }
}
</script>
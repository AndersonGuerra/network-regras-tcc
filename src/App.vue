<template>
  <div id="app">
    <div id="network"></div>
    <div>
      <h1>Conexões</h1>
      <div v-if="selectedNode.node">
        <p>Regra Selecionada: {{ selectedNode.node }}</p>
        <p>
          Conexões:
          <span
            v-for="(connection, index) in selectedNode.connections"
            :key="index"
          >
            {{ connection
            }}<span v-if="index != selectedNode.connections.length - 1">,</span>
          </span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import { Network } from "vis-network/peer/esm/vis-network";
import { DataSet } from "vis-data/peer/esm/vis-data";
export default {
  name: "App",
  components: {},
  data() {
    return {
      rawEdges: null,
      selectedNode: {},
    };
  },
  mounted() {
    const rulesNumbers = [
      1, 2, 10, 13, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 32, 33,
      34, 40, 41, 42, 43, 44, 46, 47, 48, 49, 51, 53, 55, 59, 60, 61, 63, 64,
      65, 66, 70, 71, 73, 75, 84, 85,
    ];
    const nodes = new DataSet(
      rulesNumbers.map((e) => {
        return {
          id: `r${e}`,
          label: `Regra ${e}`,
        };
      })
    );
    const rawEdges = [
      { from: "r1", to: "r2" },
      { from: "r1", to: "r26" },
      { from: "r1", to: "r41" },
      { from: "r2", to: "r1" },
      { from: "r2", to: "r26" },
      { from: "r2", to: "r41" },
      { from: "r10", to: "r21" },
      { from: "r13", to: "r18" },
      { from: "r13", to: "r34" },
      { from: "r16", to: "r17" },
      { from: "r17", to: "r16" },
      { from: "r18", to: "r13" },
      { from: "r19", to: "r40" },
      { from: "r19", to: "r55" },
      { from: "r20", to: "r21" },
      { from: "r20", to: "r22" },
      { from: "r20", to: "r63" },
      { from: "r21", to: "r20" },
      { from: "r21", to: "r63" },
      { from: "r22", to: "r20" },
      { from: "r22", to: "r21" },
      { from: "r22", to: "r63" },
      { from: "r23", to: "r84" },
      { from: "r23", to: "r85" },
      { from: "r26", to: "r1" },
      { from: "r26", to: "r2" },
      { from: "r26", to: "r55" },
      { from: "r28", to: "r40" },
      { from: "r29", to: "r40" },
      { from: "r29", to: "r73" },
      { from: "r29", to: "r75" },
      { from: "r30", to: "r40" },
      { from: "r31", to: "r32" },
      { from: "r31", to: "r51" },
      { from: "r31", to: "r53" },
      { from: "r32", to: "r31" },
      { from: "r32", to: "r51" },
      { from: "r32", to: "r53" },
      { from: "r33", to: "r34" },
      { from: "r34", to: "r13" },
      { from: "r34", to: "r33" },
      { from: "r40", to: "r19" },
      { from: "r40", to: "r20" },
      { from: "r40", to: "r21" },
      { from: "r40", to: "r22" },
      { from: "r40", to: "r28" },
      { from: "r40", to: "r29" },
      { from: "r40", to: "r30" },
      { from: "r41", to: "r1" },
      { from: "r41", to: "r2" },
      { from: "r42", to: "r40" },
      { from: "r43", to: "r47" },
      { from: "r44", to: "r18" },
      { from: "r44", to: "r34" },
      { from: "r44", to: "r40" },
      { from: "r44", to: "r42" },
      { from: "r46", to: "r18" },
      { from: "r46", to: "r42" },
      { from: "r46", to: "r44" },
      { from: "r47", to: "r41" },
      { from: "r47", to: "r42" },
      { from: "r47", to: "r43" },
      { from: "r47", to: "r44" },
      { from: "r47", to: "r46" },
      { from: "r47", to: "r49" },
      { from: "r48", to: "r40" },
      { from: "r48", to: "r47" },
      { from: "r49", to: "r47" },
      { from: "r51", to: "r31" },
      { from: "r51", to: "r32" },
      { from: "r51", to: "r53" },
      { from: "r53", to: "r31" },
      { from: "r53", to: "r32" },
      { from: "r53", to: "r51" },
      { from: "r55", to: "r19" },
      { from: "r55", to: "r26" },
      { from: "r55", to: "r59" },
      { from: "r59", to: "r55" },
      { from: "r60", to: "r40" },
      { from: "r60", to: "r47" },
      { from: "r61", to: "r47" },
      { from: "r63", to: "r20" },
      { from: "r63", to: "r21" },
      { from: "r63", to: "r22" },
      { from: "r64", to: "r20" },
      { from: "r64", to: "r21" },
      { from: "r64", to: "r22" },
      { from: "r64", to: "r40" },
      { from: "r64", to: "r63" },
      { from: "r65", to: "r20" },
      { from: "r65", to: "r21" },
      { from: "r65", to: "r22" },
      { from: "r65", to: "r40" },
      { from: "r65", to: "r63" },
      { from: "r66", to: "r65" },
      { from: "r70", to: "r26" },
      { from: "r70", to: "r55" },
      { from: "r71", to: "r22" },
      { from: "r71", to: "r63" },
      { from: "r73", to: "r29" },
      { from: "r73", to: "r75" },
      { from: "r75", to: "r29" },
      { from: "r75", to: "r73" },
      { from: "r84", to: "r23" },
      { from: "r84", to: "r85" },
      { from: "r85", to: "r23" },
      { from: "r85", to: "r84" },
    ];
    this.rawEdges = rawEdges;
    const refinedEdges = [];
    for (let i = 0; i < rawEdges.length; i++) {
      const edge = rawEdges[i];
      const repeated = refinedEdges.filter((e) => {
        if (e.to === edge.from && e.from === edge.to) return true;
        else return false;
      });
      if (repeated.length === 0) refinedEdges.push(edge);
    }
    const edges = new DataSet(refinedEdges);
    const container = document.getElementById("network");
    const data = {
      nodes,
      edges,
    };
    const options = {};
    const network = new Network(container, data, options);
    network.on("click", this.getNode);
  },
  methods: {
    getConnections(node) {
      const connections = [];
      for (let i = 0; i < this.rawEdges.length; i++) {
        const rawEdge = this.rawEdges[i];
        if (rawEdge.from === node && !connections.includes(rawEdge.to)) {
          connections.push(rawEdge.to);
        } else if (rawEdge.to === node && !connections.includes(rawEdge.from)) {
          connections.push(rawEdge.from);
        }
      }
      return connections;
    },
    getNode(params) {
      if (params.nodes.length === 1) {
        const node = params.nodes[0];
        const connections = this.getConnections(node);
        const data = {
          node,
          connections,
        };
        this.selectedNode = data;
      }
    },
  },
};
</script>

<style>
#network {
  width: 100%;
  height: 480px;
  border: 1px solid lightgrey;
}
</style>

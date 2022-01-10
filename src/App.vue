<template>
  <div class="container" id="app">
    <div class="mb-3 mt-3" id="network"></div>
    <div class="form-group">
      <div class="row">
        <label for="exampleFormControlFile1">Escolha uma planilha</label>
      </div>
      <input
        @change="selectFile"
        type="file"
        class="form-control-file"
        id="exampleFormControlFile1"
      />
    </div>
    <mainconnection-card :selectedNode="selectedNode" />
  </div>
</template>

<script>
import { Network } from "vis-network/peer/esm/vis-network";
import { DataSet } from "vis-data/peer/esm/vis-data";
import MainconnectionCard from "./components/MainconnectionCard.vue";
import xlsx from "xlsx";
const reader = new FileReader();

export default {
  name: "App",
  components: { MainconnectionCard },
  data() {
    return {
      workbook: null,
      rawEdges: null,
      rulesNumbers: null,
      selectedNode: {},
    };
  },
  watch: {
    workbook: function (val) {
      if (val !== null) {
        const sheet1Name = this.workbook.SheetNames[0];
        const sheet1 = xlsx.utils.sheet_to_json(
          this.workbook.Sheets[sheet1Name]
        );
        const sheet2Name = this.workbook.SheetNames[1];
        const sheet2 = xlsx.utils.sheet_to_json(
          this.workbook.Sheets[sheet2Name]
        );
        const rawEdges = [];
        const rulesNumbers = [];
        for (let i = 0; i < sheet1.length; i++) {
          const linha = sheet1[i];
          const regra = linha["__EMPTY"];
          for (let coluna in linha) {
            if (coluna !== "__EMPTY" && regra !== coluna) {
              rawEdges.push({
                from: regra,
                to: coluna,
              });
            }
          }
          if (linha["__EMPTY"] !== undefined) {
            const description = sheet2.filter((e) => e.ID === linha["__EMPTY"]);
            rulesNumbers.push({
              id: linha["__EMPTY"],
              label: `Regra ${linha["__EMPTY"]}`,
              description:
                description.length > 0 ? description[0]["DESCRIÇÃO"] : "",
            });
          }
        }
        this.rulesNumbers = rulesNumbers;
        this.rawEdges = rawEdges;
        const refinedEdges = [];
        const container = document.getElementById("network");
        for (let i = 0; i < rawEdges.length; i++) {
          const edge = rawEdges[i];
          const repeated = refinedEdges.filter((e) => {
            if (e.to === edge.from && e.from === edge.to) return true;
            else return false;
          });
          if (repeated.length === 0) refinedEdges.push(edge);
        }
        const nodes = new DataSet(rulesNumbers);
        const edges = new DataSet(refinedEdges);
        const data = {
          nodes,
          edges,
        };
        const options = {};
        const network = new Network(container, data, options);
        network.on("click", this.getNode);
      }
    },
  },
  created() {
    reader.onload = (e) => {
      const data = e.target.result;
      this.workbook = xlsx.read(data, { type: "binary" });
    };
  },
  mounted() {},
  methods: {
    selectFile(event) {
      reader.readAsBinaryString(event.target.files[0]);
    },
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
        let connections = this.getConnections(node).filter(
          (e) => e !== undefined
        );
        connections = connections.map((e) => {
          const subconnections = this.getConnections(e);
          return {
            node: e,
            connections: subconnections,
          };
        });
        const nodeDetails = this.rulesNumbers.filter((e) => e.id === node);
        const data = {
          node: node,
          connections,
          description: nodeDetails.length > 0 ? nodeDetails[0].description : "",
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
@import "~bootstrap/dist/css/bootstrap.css";
</style>

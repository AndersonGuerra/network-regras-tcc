<template>
  <div>
    <div class="card mb-3" v-if="selectedNode.node">
      <div class="card-body">
        <h3 class="card-title">
          {{ selectedNode.node }}: {{ selectedNode.description }}
        </h3>
        <h4 class="card-subtitle mb-2 text-muted">
          Conexões de {{ selectedNode.node }}:
        </h4>
        <div class="row">
          <div
            class="col"
            v-for="(connection, index) in selectedNode.connections"
            :key="index"
          >
            <subconnection-card :connection="connection" />
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <h5>Selecione uma regra para visualizar suas conexões</h5>
    </div>
  </div>
</template>
<script>
import SubconnectionCard from "./SubconnectionCard.vue";
export default {
  components: { SubconnectionCard },
  name: "MainConnectionCard",
  props: ["selectedNode"],
};
</script>
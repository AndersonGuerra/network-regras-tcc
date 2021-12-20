<template>
  <div class="card mb-3">
    <div class="card-body">
      <h5 class="card-title">{{ connection.node }}</h5>
      <h6 class="card-subtitle mb-2 text-muted">
        Conexões de {{ connection.node }}:
      </h6>
      <p class="card-text">
        <span v-for="(sub, index) in connection.connections" :key="index">
          {{ sub }}
        </span>
      </p>
    </div>
  </div>
</template>

<script>
export default {
  name: "SubconnectionCard",
  props: ["connection"],
};
</script>
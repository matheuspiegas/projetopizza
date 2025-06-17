<template>
  <div class="main-container">
    <DashPedidos />
  </div>
</template>

<script>
import DashPedidos from "@/components/DashPedidos.vue";

export default {
  name: "PedidosView",
  components: { DashPedidos },
  data() {
    return {};
  },
  methods: {
    // Aqui você pode adicionar métodos para manipular pedidos
  },
};
</script>

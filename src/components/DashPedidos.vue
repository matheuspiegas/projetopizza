<template>
  <div class="table-container">
    <h2>Pedidos de Pizza</h2>
    <table id="pizza-table">
      <thead>
        <tr id="pizza-table-heading">
          <th class="order-id">#</th>
          <th>Cliente</th>
          <th>Massa</th>
          <th>Sabor</th>
          <th>Opcionais</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody id="pizza-table-rows">
        <tr v-for="pizza in pizzas" :key="pizza.id" class="table-row">
          <td class="order-number">{{ pizza.id }}</td>
          <td class="client-name">{{ pizza.cliente }}</td>
          <td>{{ pizza.massa }}</td>
          <td class="flavor">{{ pizza.sabor }}</td>
          <td class="optionals">
            <ul v-if="pizza.opcionais && pizza.opcionais.length > 0">
              <li v-for="opcional in pizza.opcionais" :key="opcional">
                {{ opcional }}
              </li>
            </ul>
            <span v-else class="no-optionals">Nenhum</span>
          </td>
          <td class="actions">
            <select
              name="status"
              class="status-select"
              v-model="pizza.status"
              @change="updateOrderStatus(pizza.id, pizza.status)"
            >
              <option value="" disabled>Selecione o status</option>
              <option
                v-for="statusItem in status"
                :key="statusItem.id"
                :value="statusItem.tipo"
              >
                {{ statusItem.tipo }}
              </option>
            </select>
            <button @click="showCancelModal(pizza)" class="cancel-btn">
              Cancelar
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Modal de Confirmação -->
    <div v-if="showModal" class="modal-overlay" @click="closeModal">
      <div class="modal" @click.stop>
        <div class="modal-header">
          <h3>Confirmar Cancelamento</h3>
        </div>
        <div class="modal-body">
          <p>
            Tem certeza que deseja cancelar o pedido de
            <strong>{{ selectedPizza?.cliente }}</strong
            >?
          </p>
          <p>
            <strong>Pizza:</strong> {{ selectedPizza?.sabor }} ({{
              selectedPizza?.massa
            }})
          </p>
        </div>
        <div class="modal-footer">
          <button @click="closeModal" class="btn-secondary">Cancelar</button>
          <button @click="confirmCancel" class="btn-danger">Confirmar</button>
        </div>
      </div>
    </div>

    <!-- Toast Notification -->
    <div v-if="toast.show" :class="['toast', toast.type]">
      <div class="toast-content">
        <span class="toast-icon">{{
          toast.type === "success" ? "✓" : "⚠"
        }}</span>
        <span class="toast-message">
          Pizza de <strong>{{ toast.cliente }}</strong> com id
          <strong>{{ toast.id }}</strong> atualizada para {{ toast.status }}!
        </span>
      </div>
    </div>
  </div>
</template>

<script>
import { api } from "../../lib/axios";

export default {
  name: "DashPedidos",
  data() {
    return {
      pizzas: [],
      status: [],
      ingredientes: [],
      showModal: false,
      selectedPizza: null,
      toast: {
        show: false,
        cliente: "",
        id: "",
        status: "",
        type: "success", // success ou error
      },
    };
  },
  async mounted() {
    await this.getOrders();
    await this.getStatus();
  },
  methods: {
    async getOrders() {
      const response = await api.get("/pizzas");
      this.pizzas = response.data;
    },
    async getStatus() {
      const response = await api.get("/status");
      this.status = response.data;
    },

    async updateOrderStatus(pizzaId, status) {
      try {
        const { data } = await api.patch(`/pizzas/${pizzaId}`, {
          status: status,
        });
        this.getOrders();
        console.log(data);
        this.showToast(data.cliente, data.id, status, "success");
      } catch (error) {
        console.error("Error updating order status:", error);
      }
    },

    showCancelModal(pizza) {
      this.selectedPizza = pizza;
      this.showModal = true;
    },

    closeModal() {
      this.showModal = false;
      this.selectedPizza = null;
    },

    async confirmCancel() {
      try {
        await api.delete(`/pizzas/${this.selectedPizza.id}`);
        this.showToast("Pizza cancelada com sucesso!", "success");
        this.getOrders();
      } catch (error) {
        console.error("Error cancelling order:", error);
        this.showToast("Erro ao cancelar pizza. Tente novamente.", "error");
      } finally {
        this.closeModal();
      }
    },

    showToast(cliente, id, status, type) {
      this.toast = {
        show: true,
        cliente: cliente,
        id: id,
        status: status,
        type: type,
      };

      setTimeout(() => {
        this.toast.show = false;
      }, 2000);
    },
  },
};
</script>

<style scoped>
.table-container {
  padding: 2rem;
  background-color: #f8f9fa;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  margin: 1rem;
}

.table-container h2 {
  color: #333;
  margin-bottom: 1.5rem;
  text-align: center;
  font-size: 1.8rem;
  font-weight: 600;
}

#pizza-table {
  width: 100%;
  border-collapse: collapse;
  background-color: white;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

thead {
  background: linear-gradient(135deg, #ff6b35, #f7931e);
  color: white;
}

thead th {
  padding: 1rem;
  text-align: left;
  font-weight: 600;
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.table-row {
  transition: all 0.3s ease;
}

.table-row:nth-child(even) {
  background-color: #f8f9fa;
}

.table-row:hover {
  background-color: #e3f2fd;
  transform: translateY(-1px);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

td {
  padding: 1rem;
  border-bottom: 1px solid #e0e0e0;
  vertical-align: top;
}

.order-number {
  font-weight: bold;
  color: #ff6b35;
  font-size: 1.1rem;
}

.client-name {
  font-weight: 600;
  color: #333;
}

.flavor {
  color: #555;
  font-style: italic;
}

.optionals ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.optionals li {
  background-color: #fff3e0;
  color: #e65100;
  padding: 0.25rem 0.5rem;
  margin: 0.25rem 0;
  border-radius: 12px;
  font-size: 0.85rem;
  border-left: 3px solid #ff9800;
}

.no-optionals {
  color: #999;
  font-style: italic;
  font-size: 0.9rem;
}

.actions {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  align-items: stretch;
}

.status-select {
  padding: 0.5rem;
  border: 2px solid #e0e0e0;
  border-radius: 6px;
  background-color: white;
  color: #333;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.status-select:focus {
  outline: none;
  border-color: #ff6b35;
  box-shadow: 0 0 0 3px rgba(255, 107, 53, 0.1);
}

.cancel-btn {
  background: linear-gradient(135deg, #f44336, #d32f2f);
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.9rem;
  font-weight: 500;
  transition: all 0.3s ease;
}

.cancel-btn:hover {
  background: linear-gradient(135deg, #d32f2f, #b71c1c);
  transform: translateY(-1px);
  box-shadow: 0 4px 8px rgba(244, 67, 54, 0.3);
}

.cancel-btn:active {
  transform: translateY(0);
}

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease;
}

.modal {
  background: white;
  border-radius: 12px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  width: 90%;
  max-width: 500px;
  animation: slideIn 0.3s ease;
}

.modal-header {
  padding: 1.5rem;
  border-bottom: 1px solid #e0e0e0;
  background: linear-gradient(135deg, #ff6b35, #f7931e);
  color: white;
  border-radius: 12px 12px 0 0;
}

.modal-header h3 {
  margin: 0;
  font-size: 1.25rem;
  font-weight: 600;
}

.modal-body {
  padding: 2rem 1.5rem;
  color: #333;
  line-height: 1.6;
}

.modal-footer {
  padding: 1rem 1.5rem;
  border-top: 1px solid #e0e0e0;
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
  background-color: #f8f9fa;
  border-radius: 0 0 12px 12px;
}

.btn-secondary {
  background-color: #6c757d;
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.3s ease;
}

.btn-secondary:hover {
  background-color: #5a6268;
  transform: translateY(-1px);
}

.btn-danger {
  background: linear-gradient(135deg, #f44336, #d32f2f);
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.3s ease;
}

.btn-danger:hover {
  background: linear-gradient(135deg, #d32f2f, #b71c1c);
  transform: translateY(-1px);
  box-shadow: 0 4px 8px rgba(244, 67, 54, 0.3);
}

/* Toast Styles */
.toast {
  position: fixed;
  top: 20px;
  right: 20px;
  min-width: 300px;
  padding: 1rem 1.5rem;
  border-radius: 8px;
  color: white;
  font-weight: 500;
  z-index: 1001;
  animation: slideInRight 0.3s ease;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
}

.toast.success {
  background: linear-gradient(135deg, #4caf50, #45a049);
}

.toast.error {
  background: linear-gradient(135deg, #f44336, #d32f2f);
}

.toast-content {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.toast-icon {
  font-size: 1.2rem;
  font-weight: bold;
}

/* Animations */
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-50px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes slideInRight {
  from {
    opacity: 0;
    transform: translateX(100px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

/* Responsividade */
@media (max-width: 768px) {
  .table-container {
    padding: 1rem;
    margin: 0.5rem;
  }

  #pizza-table {
    font-size: 0.8rem;
  }

  th,
  td {
    padding: 0.5rem;
  }

  .actions {
    flex-direction: column;
    gap: 0.25rem;
  }

  .modal {
    width: 95%;
    margin: 1rem;
  }

  .toast {
    right: 10px;
    left: 10px;
    min-width: auto;
  }
}
</style>

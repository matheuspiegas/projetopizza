<template>
  <div class="container">
    <div class="form-container">
      <h1>Monte sua pizza</h1>
      <Form @submit="createOrder" v-slot="{ meta }">
        <div class="form-group">
          <label for="nome">Nome do cliente:</label>
          <Field
            type="text"
            name="cliente"
            placeholder="Digite o seu nome"
            :rules="required"
            class="form-input"
          />
        </div>

        <div class="form-group">
          <label for="massa">Escolha a massa:</label>
          <Field name="massa" as="select" :rules="required" class="form-select">
            <option disabled value="">Selecione uma massa</option>
            <option value="Tradicional">Tradicional</option>
            <option value="Fina">Fina</option>
            <option value="Integral">Integral</option>
          </Field>
        </div>

        <div class="form-group">
          <label for="sabor">Escolha o sabor da sua pizza:</label>
          <Field as="select" name="sabor" :rules="required" class="form-select">
            <option disabled value="">Selecione um sabor</option>
            <option value="Calabresa">Calabresa</option>
            <option value="Quatro queijos">Quatro Queijos</option>
            <option value="Portuguesa">Portuguesa</option>
            <option value="Margherita">Margherita</option>
            <option value="Mussarela">Mussarela</option>
            <option value="Frango com Catupiri">Frango com Catupiri</option>
          </Field>
        </div>

        <div class="form-group checkbox-group">
          <label class="checkbox-label">
            <Field
              name="borda"
              type="checkbox"
              v-model="bordaCatupiry"
              :value="true"
              :unchecked-value="false"
              class="checkbox-input"
            />
            <span class="checkbox-text">Borda com catupiry</span>
          </label>
        </div>

        <button
          type="submit"
          class="submit-btn"
          :disabled="!meta.valid"
          :class="{ disabled: !meta.valid }"
        >
          Criar minha pizza!
        </button>
      </Form>

      <!-- Toast Notification -->
      <div v-if="toast.show" :class="['toast', toast.type]">
        <div class="toast-content">
          <span class="toast-icon">{{
            toast.type === "success" ? "✓" : "⚠"
          }}</span>
          <span class="toast-message">{{ toast.message }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Field, Form } from "vee-validate";
import { api } from "../../lib/axios";

export default {
  name: "HomeComponent",
  components: {
    Field,
    Form,
  },
  data() {
    return {
      bordaCatupiry: false,
      toast: {
        show: false,
        message: "",
        type: "success",
      },
    };
  },
  methods: {
    required(value) {
      if (!value || value === "") {
        return "Este campo é obrigatório";
      }
      return true;
    },

    async createOrder(values, { resetForm }) {
      try {
        await api.post("/pizzas", {
          cliente: values.cliente,
          massa: values.massa,
          sabor: values.sabor,
          bordaCatupiry: values.borda,
          status: "Solicitado",
        });

        this.showToast("Pizza criada com sucesso!", "success");
        resetForm();
      } catch (error) {
        console.error("Erro ao criar pizza:", error);
        this.showToast("Erro ao criar pizza. Tente novamente.", "error");
      }
    },

    showToast(message, type) {
      this.toast.message = message;
      this.toast.type = type;
      this.toast.show = true;

      setTimeout(() => {
        this.toast.show = false;
      }, 3000);
    },
  },
};
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 80vh;
  padding: 2rem;
}

.form-container {
  background-color: #f8f9fa;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  padding: 2rem;
  width: 100%;
  max-width: 500px;
  position: relative;
}

h1 {
  color: #333;
  margin-bottom: 2rem;
  text-align: center;
  font-size: 2rem;
  font-weight: 600;
  background: linear-gradient(135deg, #ff6b35, #f7931e);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  color: #333;
  font-weight: 600;
  font-size: 0.9rem;
  border-left: 3px solid #ff6b35;
  padding-left: 0.75rem;
}

.form-input,
.form-select {
  width: 100%;
  padding: 0.75rem;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  background-color: white;
  color: #333;
  font-size: 1rem;
  transition: all 0.3s ease;
  box-sizing: border-box;
}

.form-input:focus,
.form-select:focus {
  outline: none;
  border-color: #ff6b35;
  box-shadow: 0 0 0 3px rgba(255, 107, 53, 0.1);
}

.form-input::placeholder {
  color: #999;
}

.checkbox-group {
  background-color: white;
  padding: 1rem;
  border-radius: 8px;
  border: 2px solid #e0e0e0;
  transition: all 0.3s ease;
}

.checkbox-group:hover {
  border-color: #ff6b35;
}

.checkbox-label {
  display: flex;
  align-items: center;
  cursor: pointer;
  border: none !important;
  padding: 0 !important;
  margin: 0;
}

.checkbox-input {
  width: 18px;
  height: 18px;
  margin-right: 0.75rem;
  accent-color: #ff6b35;
  cursor: pointer;
}

.checkbox-text {
  color: #333;
  font-weight: 500;
  user-select: none;
}

.submit-btn {
  width: 100%;
  background: linear-gradient(135deg, #ff6b35, #f7931e);
  color: white;
  border: none;
  padding: 1rem 2rem;
  border-radius: 8px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-top: 1rem;
}

.submit-btn:hover:not(:disabled) {
  background: linear-gradient(135deg, #f7931e, #ff6b35);
  transform: translateY(-2px);
  box-shadow: 0 6px 12px rgba(255, 107, 53, 0.3);
}

.submit-btn:active:not(:disabled) {
  transform: translateY(0);
}

.submit-btn:disabled,
.submit-btn.disabled {
  background: linear-gradient(135deg, #ccc, #999);
  cursor: not-allowed;
  opacity: 0.6;
}

.submit-btn:disabled:hover,
.submit-btn.disabled:hover {
  transform: none;
  box-shadow: none;
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
  .container {
    padding: 1rem;
  }

  .form-container {
    padding: 1.5rem;
  }

  h1 {
    font-size: 1.5rem;
  }

  .form-input,
  .form-select {
    padding: 0.65rem;
    font-size: 0.9rem;
  }

  .submit-btn {
    padding: 0.85rem 1.5rem;
    font-size: 1rem;
  }

  .toast {
    right: 10px;
    left: 10px;
    min-width: auto;
  }
}

@media (max-width: 480px) {
  .form-container {
    padding: 1rem;
  }

  .form-group {
    margin-bottom: 1rem;
  }
}
</style>

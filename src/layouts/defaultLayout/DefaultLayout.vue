<template>
  <div class="layout">
    <Navbar />
    <main class="main-content">
      <router-view />
    </main>
    <footer>
      <div class="footer-container">
        <p>&copy; 2025 Make your pizza - Todos os direitos reservados</p>
        <div class="footer-info">
          <span>Feito com ❤️ para os amantes de pizza</span>
        </div>
      </div>
    </footer>
  </div>
</template>

<script>
import Navbar from "../../components/Navbar.vue";

export default {
  name: "Layout",
  components: {
    Navbar,
  },
};
</script>

<style scoped>
.layout {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  min-height: 100vh;
}

.main-content {
  display: flex;
  justify-content: center;
  flex: 1;
}

footer {
  background: linear-gradient(135deg, #333, #2c2c2c);
  color: white;
  padding: 2rem 0;
  margin-top: 2rem;
  border-top: 3px solid #ff6b35;
  box-shadow: 0 -2px 8px rgba(0, 0, 0, 0.1);
}

.footer-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.footer-container p {
  font-size: 1.1rem;
  font-weight: 600;
  color: #ff6b35;
  margin: 0;
  text-align: center;
}

.footer-info {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.footer-info span {
  font-size: 0.9rem;
  color: #ccc;
  font-weight: 400;
  text-align: center;
}

/* Responsividade */
@media (max-width: 768px) {
  footer {
    padding: 1.5rem 0;
  }

  .footer-container {
    padding: 0 1rem;
    gap: 0.75rem;
  }

  .footer-container p {
    font-size: 1rem;
  }

  .footer-info span {
    font-size: 0.85rem;
  }
}

@media (max-width: 480px) {
  footer {
    padding: 1rem 0;
  }

  .footer-container {
    gap: 0.5rem;
  }

  .footer-container p {
    font-size: 0.9rem;
  }

  .footer-info span {
    font-size: 0.8rem;
  }
}
</style>

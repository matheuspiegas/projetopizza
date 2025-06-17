<template>
  <nav>
    <div class="container">
      <RouterLink to="/" class="nav-link logo-link">
        <img :src="logo" alt="Logo" />
      </RouterLink>
      <div class="nav-links">
        <RouterLink to="/" class="nav-link" active-class="active"
          >Home</RouterLink
        >
        <RouterLink to="/pedidos" class="nav-link" active-class="active"
          >Pedidos</RouterLink
        >
      </div>
    </div>
  </nav>
</template>

<script>
import { RouterLink } from "vue-router";
import logo from "../../public/images/logo.png";

export default {
  name: "NavbarComponent",
  components: {
    RouterLink,
  },
  data() {
    return {
      logo: logo,
    };
  },
};
</script>

<style scoped>
nav {
  background: linear-gradient(135deg, #333, #2c2c2c);
  padding: 1rem 0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  border-bottom: 3px solid #ff6b35;
}

.container {
  display: flex;
  margin: 0 auto;
  align-items: center;
  justify-content: space-between;
  max-width: 1200px;
  padding: 0 2rem;
}

.logo-link {
  transition: all 0.3s ease;
}

.logo-link:hover {
  transform: scale(1.05);
}

nav img {
  height: 45px;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.2));
  transition: all 0.3s ease;
}

.nav-links {
  display: flex;
  gap: 2rem;
  align-items: center;
}

.nav-link {
  color: #fff;
  text-decoration: none;
  font-weight: 500;
  font-size: 1.1rem;
  padding: 0.75rem 1.5rem;
  border-radius: 8px;
  transition: all 0.3s ease;
  position: relative;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.nav-link:not(.logo-link):hover {
  background: linear-gradient(135deg, #ff6b35, #f7931e);
  color: white;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(255, 107, 53, 0.3);
}

.nav-link.active {
  background: linear-gradient(135deg, #ff6b35, #f7931e);
  color: white;
  box-shadow: 0 2px 4px rgba(255, 107, 53, 0.3);
}

.nav-link:not(.logo-link):active {
  transform: translateY(0);
}

/* Responsividade */
@media (max-width: 768px) {
  .container {
    padding: 0 1rem;
    flex-direction: column;
    gap: 1rem;
  }

  nav {
    padding: 1.5rem 0;
  }

  .nav-links {
    gap: 1rem;
    width: 100%;
    justify-content: center;
  }

  .nav-link {
    font-size: 1rem;
    padding: 0.5rem 1rem;
  }

  nav img {
    height: 35px;
  }
}

@media (max-width: 480px) {
  .nav-links {
    flex-direction: column;
    gap: 0.5rem;
  }

  .nav-link {
    width: 100%;
    text-align: center;
    padding: 0.75rem;
  }

  .container {
    gap: 1.5rem;
  }
}
</style>

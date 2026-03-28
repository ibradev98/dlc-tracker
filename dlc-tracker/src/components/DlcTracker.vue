<template>
  <div class="dlc-app">
    <header class="dlc-header">
      <h1>DLC Stock</h1>
      <div class="dlc-header-actions">
        <input
          v-model="search"
          class="dlc-search"
          type="text"
          placeholder="Rechercher un produit..."
        />
        <button class="dlc-add-btn" @click="showAddModal = true">+</button>
      </div>
    </header>
    <ul class="dlc-list">
      <li v-for="(product, idx) in filteredProducts" :key="product.id" class="dlc-list-item">
        <span class="dlc-product-name">{{ product.name }}</span>
        <div class="dlc-date-actions">
          <ExpiryCircle :expiry-date="product.date" :size="52" />
          <button class="dlc-delete-btn" @click="deleteProduct(idx)">🗑️</button>
        </div>
      </li>
      <li v-if="filteredProducts.length === 0" class="dlc-empty">Aucun produit à afficher</li>
    </ul>
    <div v-if="showAddModal" class="dlc-modal-overlay">
      <div class="dlc-modal">
        <h2>Ajouter un produit</h2>
        <input v-model="newProduct.name" type="text" placeholder="Nom du produit" />
        <input v-model="newProduct.date" type="date" />
        <button @click="addProduct">Ajouter</button>
        <button class="dlc-close-modal" @click="showAddModal = false">Fermer</button>
      </div>
    </div>
  </div>
</template>
<script>
import ExpiryCircle from './ExpiryCircle.vue'
export default {
  name: 'DlcTracker',
  components: { ExpiryCircle },
  data() {
    return {
      products: [],
      showAddModal: false,
      newProduct: { name: '', date: '' },
      search: '',
    }
  },
  computed: {
    filteredProducts() {
      if (!this.search) return this.products
      return this.products.filter((p) => p.name.toLowerCase().includes(this.search.toLowerCase()))
    },
  },
  methods: {
    addProduct() {
      if (!this.newProduct.name || !this.newProduct.date) return
      this.products.push({
        id: Date.now(),
        name: this.newProduct.name,
        date: this.newProduct.date,
      })
      this.newProduct = { name: '', date: '' }
      this.showAddModal = false
    },
    deleteProduct(idx) {
      this.products.splice(idx, 1)
    },
  },
}
</script>
<style scoped>
  .dlc-app {
    max-width: 500px;
    margin: 40px auto;
    background: rgba(255,255,255,0.18);
    border-radius: 22px;
    box-shadow: 0 8px 32px 0 rgba(31,38,135,0.18);
    padding: 32px 24px 24px 24px;
    font-family: 'Segoe UI', Arial, sans-serif;
    backdrop-filter: blur(18px) saturate(180%);
    -webkit-backdrop-filter: blur(18px) saturate(180%);
    border: 1.5px solid rgba(255,255,255,0.22);
    overflow: hidden;
  }
  .dlc-header {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    margin-bottom: 24px;
    background: rgba(255,255,255,0.12);
    border-radius: 18px;
    padding: 12px 10px 8px 10px;
    box-shadow: 0 2px 12px rgba(31,38,135,0.08);
    border: 1px solid rgba(255,255,255,0.18);
  }
.dlc-header h1 {
  margin: 0 0 12px 0;
  font-size: 2.1rem;
  color: #2d3a4b;
  letter-spacing: 1px;
}
  .dlc-header-actions {
    display: flex;
    flex-direction: row;
    width: 100%;
    gap: 10px;
    align-items: center;
    background: rgba(255,255,255,0.18);
    border-radius: 12px;
    box-shadow: 0 1px 8px rgba(31,38,135,0.06);
    padding: 4px 4px 4px 8px;
  }
  .dlc-search {
    flex: 1;
    padding: 8px 12px;
    border-radius: 8px;
    border: 1.5px solid rgba(255,255,255,0.22);
    font-size: 1rem;
    outline: none;
    background: rgba(255,255,255,0.32);
    box-shadow: 0 2px 8px rgba(31,38,135,0.06);
    color: #2d3a4b;
    transition: border 0.2s, background 0.2s;
  }
  .dlc-search:focus {
    border: 1.5px solid #6c63ff;
    background: rgba(255,255,255,0.48);
  }
.dlc-search:focus {
  border: 1.5px solid #6c63ff;
}
  .dlc-add-btn {
    background: linear-gradient(135deg, #6c63ff 60%, #a084ee 100%);
    color: #fff;
    border: none;
    border-radius: 50%;
    width: 38px;
    height: 38px;
    font-size: 1.6rem;
    cursor: pointer;
    box-shadow: 0 2px 8px rgba(108,99,255,0.18);
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background 0.2s, box-shadow 0.2s;
  }
  .dlc-add-btn:hover {
    background: linear-gradient(135deg, #5548c8 60%, #a084ee 100%);
    box-shadow: 0 4px 16px rgba(108,99,255,0.22);
  }
.dlc-list {
  list-style: none;
  padding: 0;
  margin: 0;
}
.dlc-list-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 0;
  border-bottom: 1px solid #f0f0f0;
  font-size: 1.08rem;
}
.dlc-product-name {
  flex: 1;
  text-align: left;
  color: #2d3a4b;
}
.dlc-date-actions {
  display: flex;
  align-items: center;
  gap: 10px;
}
.dlc-date-circle {
  display: inline-block;
  min-width: 48px;
  text-align: center;
  padding: 7px 0;
  border-radius: 50%;
  font-weight: bold;
  font-size: 1.05rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
  transition:
    background 0.2s,
    color 0.2s;
}
.dlc-green {
  background: #e6f9e7;
  color: #2ecc40;
  border: 2px solid #2ecc40;
}
.dlc-red {
  background: #ffeaea;
  color: #e74c3c;
  border: 2px solid #e74c3c;
}
.dlc-gray {
  background: #f4f4f4;
  color: #b0b0b0;
  border: 2px solid #b0b0b0;
}
.dlc-delete-btn {
  background: none;
  border: none;
  color: #e74c3c;
  font-size: 1.2rem;
  cursor: pointer;
  padding: 4px 6px;
  border-radius: 6px;
  transition: background 0.2s;
}
.dlc-delete-btn:hover {
  background: #ffeaea;
}
.dlc-empty {
  text-align: center;
  color: #b0b0b0;
  padding: 24px 0 0 0;
}
.dlc-modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(44, 62, 80, 0.18);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}
.dlc-modal {
  background: #fff;
  border-radius: 14px;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.13);
  padding: 32px 24px 24px 24px;
  min-width: 300px;
  display: flex;
  flex-direction: column;
  gap: 16px;
  align-items: stretch;
}
.dlc-modal input[type='text'],
.dlc-modal input[type='date'] {
  padding: 8px 12px;
  border-radius: 8px;
  border: 1px solid #e0e0e0;
  font-size: 1rem;
  outline: none;
  margin-bottom: 8px;
}
.dlc-modal button {
  background: #6c63ff;
  color: #fff;
  border: none;
  border-radius: 8px;
  padding: 10px 0;
  font-size: 1rem;
  cursor: pointer;
  margin-top: 8px;
  transition: background 0.2s;
}
.dlc-modal button:hover {
  background: #5548c8;
}
.dlc-close-modal {
  background: #e0e0e0 !important;
  color: #2d3a4b !important;
  margin-top: 0;
}
  /* Responsive pour iPhone 6 et petits écrans */
  @media (max-width: 400px) {
    .dlc-app {
      max-width: 100vw;
      padding: 12px 2vw 12px 2vw;
      border-radius: 0;
      box-shadow: none;
    }
    .dlc-header h1 {
      font-size: 1.3rem;
    }
    .dlc-header-actions {
      flex-direction: row;
      gap: 6px;
      padding: 2px 2px 2px 4px;
    }
    .dlc-search {
      font-size: 0.95rem;
      padding: 7px 8px;
    }
    .dlc-add-btn {
      width: 32px;
      height: 32px;
      font-size: 1.2rem;
    }
    .dlc-list-item {
      font-size: 0.98rem;
      padding: 8px 0;
    }
    .dlc-date-actions {
      gap: 6px;
    }
    .dlc-modal {
      min-width: unset;
      width: 95vw;
      padding: 18px 8px 12px 8px;
    }
    .dlc-modal input[type='text'],
    .dlc-modal input[type='date'] {
      font-size: 0.95rem;
      padding: 7px 8px;
    }
    .dlc-modal button {
      font-size: 0.95rem;
      padding: 8px 0;
    }
  }
</style>

<template>
  <div class="dlc-bg">
    <div class="dlc-card">
      <!-- Header -->
      <div class="dlc-header-premium">
        <div class="dlc-header-top">
          <span class="icon menu-icon">&#9632;&#9632;<br />&#9632;&#9632;</span>
          <span class="header-date">{{ todayDate }}</span>
          <span class="icon clock-icon">&#128337;</span>
        </div>
        <div class="dlc-header-bottom">
          <div class="header-title">
            <span class="title">DLC Stock</span>
            <span class="subtitle">{{ filteredProducts.length }} Produits</span>
          </div>
          <button class="add-btn" @click="showAddModal = true">Add New</button>
        </div>
      </div>
      <!-- Days Row (Semaine) -->
      <div class="days-row">
        <div
          v-for="(day, i) in days"
          :key="i"
          :class="['day-block', { active: i === selectedDay } ]"
          @click="selectedDay = i"
        >
          <span class="day-num">{{ day.num }}</span>
          <span class="day-label">{{ day.label }}</span>
        </div>
      </div>
      <!-- Liste des produits (tâches) -->
      <div class="tasks-section">
        <div class="tasks-title">Mes produits</div>
        <div
          v-for="(product, idx) in filteredProducts"
          :key="product.id"
          :class="['task-item', { active: idx === selectedProduct }]"
          @click="selectedProduct = idx"
        >
          <div class="task-time">{{ formatDate(product.date) }}</div>
          <div class="task-main">
            <div class="task-title">{{ product.name }}</div>
            <div class="task-desc">À consommer avant</div>
          </div>
          <div class="task-check" @click.stop="deleteProduct(idx)">
            <span class="checkbox delete-btn">🗑️</span>
          </div>
        </div>
        <div v-if="filteredProducts.length === 0" class="dlc-empty">Aucun produit à afficher</div>
      </div>
    </div>
    <!-- Modal d'ajout -->
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
export default {
  name: 'DlcTracker',
  data() {
    return {
      todayDate: this.getToday(),
      days: [
        { num: 25, label: 'Lun' },
        { num: 26, label: 'Mar' },
        { num: 27, label: 'Mer' },
        { num: 28, label: 'Jeu' },
        { num: 29, label: 'Ven' },
        { num: 30, label: 'Sam' },
        { num: 31, label: 'Dim' },
      ],
      selectedDay: new Date().getDay() - 1,
      selectedProduct: 0,
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
    getToday() {
      const d = new Date()
      return d.toLocaleDateString('fr-FR', { day: '2-digit', month: 'short' })
    },
    formatDate(date) {
      if (!date) return ''
      const d = new Date(date)
      return d.toLocaleDateString('fr-FR', { day: '2-digit', month: '2-digit' })
    },
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
.dlc-bg {
  min-height: 100vh;
  background: linear-gradient(135deg, #e9e4f0 0%, #c7c5f4 60%, #a084ee 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
}
  .dlc-card {
    width: 360px;
    max-width: 98vw;
    min-width: 240px;
    min-height: 485px;
    border-radius: 36px;
    background: rgba(255,255,255,0.85);
    box-shadow: 0 8px 32px 0 rgba(31,38,135,0.18);
    padding: 28px 18px 18px 18px;
    display: flex;
    flex-direction: column;
    gap: 18px;
    position: relative;
  }
.dlc-header-premium {
  background: linear-gradient(135deg, #7f53ac 0%, #647dee 100%);
  border-radius: 28px;
  padding: 18px 18px 14px 18px;
  box-shadow: 0 2px 12px rgba(127,83,172,0.08);
  color: #fff;
  margin-bottom: 8px;
}
.dlc-header-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
}
.icon {
  font-size: 1.3rem;
  opacity: 0.85;
  min-width: 32px;
  text-align: center;
}
.menu-icon {
  font-weight: bold;
  letter-spacing: 1px;
  line-height: 1.1;
}
.clock-icon {
  font-size: 1.4rem;
}
.header-date {
  font-size: 1.1rem;
  font-weight: 500;
  letter-spacing: 1px;
  opacity: 0.92;
}
.dlc-header-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-top: 2px;
}
.header-title {
  display: flex;
  flex-direction: column;
  gap: 2px;
}
.title {
  font-size: 2.1rem;
  font-weight: 700;
  color: #fff;
  letter-spacing: 1px;
}
.subtitle {
  font-size: 1.05rem;
  color: #fff;
  opacity: 0.7;
  font-weight: 400;
  margin-top: 2px;
}
.add-btn {
  background: #fff;
  color: #7f53ac;
  font-weight: 600;
  border: none;
  border-radius: 16px;
  padding: 8px 22px;
  font-size: 1.08rem;
  box-shadow: 0 2px 8px rgba(127,83,172,0.10);
  cursor: pointer;
  transition: background 0.2s, color 0.2s, box-shadow 0.2s;
}
.add-btn:hover {
  background: #f3eaff;
  color: #5e3a9c;
  box-shadow: 0 4px 16px rgba(127,83,172,0.16);
}
.days-row {
  display: flex;
  gap: 10px;
  margin: 0 0 8px 0;
  justify-content: space-between;
}
.day-block {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 44px;
  height: 56px;
  border-radius: 18px;
  background: #f3f3fa;
  color: #7f53ac;
  font-weight: 500;
  font-size: 1.08rem;
  box-shadow: 0 1px 4px rgba(127,83,172,0.06);
  cursor: pointer;
  transition: background 0.2s, color 0.2s;
}
.day-block.active {
  background: linear-gradient(135deg, #7f53ac 0%, #647dee 100%);
  color: #fff;
  box-shadow: 0 2px 8px rgba(127,83,172,0.13);
}
.day-num {
  font-size: 1.15rem;
  font-weight: 700;
}
.day-label {
  font-size: 0.95rem;
  opacity: 0.8;
}
.tasks-section {
  margin-top: 8px;
}
.tasks-title {
  font-size: 1.18rem;
  font-weight: 600;
  color: #7f53ac;
  margin-bottom: 10px;
  letter-spacing: 1px;
}
.task-item {
  display: flex;
  align-items: center;
  background: #f7f7fb;
  border-radius: 18px;
  box-shadow: 0 2px 8px rgba(127,83,172,0.07);
  padding: 14px 14px 14px 16px;
  margin-bottom: 12px;
  transition: background 0.2s, color 0.2s;
  cursor: pointer;
}
.task-item.active {
  background: linear-gradient(135deg, #7f53ac 0%, #647dee 100%);
  color: #fff;
  box-shadow: 0 4px 16px rgba(127,83,172,0.13);
}
.task-time {
  min-width: 80px;
  font-size: 1.05rem;
  font-weight: 500;
  color: #7f53ac;
  opacity: 0.85;
  margin-right: 12px;
}
.task-item.active .task-time {
  color: #fff;
  opacity: 1;
}
.task-main {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 2px;
}
.task-title {
  font-size: 1.13rem;
  font-weight: 700;
  margin-bottom: 2px;
}
.task-desc {
  font-size: 0.98rem;
  color: #7f53ac;
  opacity: 0.7;
  font-weight: 400;
}
.task-item.active .task-title,
.task-item.active .task-desc {
  color: #fff;
  opacity: 1;
}
.task-check {
  margin-left: 14px;
  display: flex;
  align-items: center;
}
.checkbox {
  width: 26px;
  height: 26px;
  border-radius: 8px;
  border: 2.5px solid #7f53ac;
  background: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.2s, border 0.2s;
  font-size: 1.3rem;
  cursor: pointer;
}
.checkbox.delete-btn {
  background: #fff;
  color: #e74c3c;
  border: 2.5px solid #e74c3c;
  font-size: 1.2rem;
  padding: 0;
  width: 32px;
  height: 32px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 1px 4px rgba(231,76,60,0.08);
  margin-left: 6px;
  transition: background 0.2s, color 0.2s;
}
.checkbox.delete-btn:hover {
  background: #ffeaea;
  color: #fff;
  border: 2.5px solid #e74c3c;
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
.dlc-modal input[type='text'] {
  padding: 8px 12px;
  border-radius: 8px;
  border: 1px solid #e0e0e0;
  font-size: 0.5rem;
  outline: none;
  margin-bottom: 8px;
}
.dlc-modal input[type='date'] {
  padding: 8px 12px;
  border-radius: 8px;
  border: 1px solid #e0e0e0;
  font-size: 0.5rem;
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
@media (max-width: 430px) {
  .dlc-card {
    width: 240px;
    min-width: 240px;
    max-width: 98vw;
    height: 485px;
    min-height: 485px;
    max-height: 98vh;
    padding: 10px 6px 8px 6px;
    border-radius: 24px;
    overflow-y: auto;
  }
  .dlc-header-premium {
    border-radius: 18px;
    padding: 12px 8px 8px 8px;
  }
  .days-row {
    gap: 4px;
  }
  .day-block {
    width: 32px;
    height: 38px;
    border-radius: 10px;
    font-size: 0.95rem;
  }
  .task-item {
    border-radius: 10px;
    padding: 8px 4px 8px 6px;
    margin-bottom: 6px;
  }
  .checkbox {
    width: 18px;
    height: 18px;
    border-radius: 5px;
    font-size: 1rem;
  }
}
</style>

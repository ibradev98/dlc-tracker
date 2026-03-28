<template>
  <div
    class="expiry-circle"
    :style="circleStyle"
    title="Date de péremption : {{ formattedDate }}"
  >
    <span class="expiry-date">{{ formattedDate }}</span>
  </div>
</template>

<script>
export default {
  name: 'ExpiryCircle',
  props: {
    expiryDate: {
      type: String,
      required: true,
    },
    size: {
      type: [String, Number],
      default: 56,
    },
  },
  computed: {
    formattedDate() {
      const d = new Date(this.expiryDate)
      return d.toLocaleDateString('fr-FR', { day: '2-digit', month: '2-digit' })
    },
    daysLeft() {
      const today = new Date()
      today.setHours(0, 0, 0, 0)
      const expiry = new Date(this.expiryDate)
      expiry.setHours(0, 0, 0, 0)
      return Math.ceil((expiry - today) / (1000 * 60 * 60 * 24))
    },
    circleColor() {
      // Vert (#2ecc40) → Jaune (#ffe066) → Orange (#ff9800) → Rouge (#e74c3c)
      const colorStops = [
        { days: 7, color: [46, 204, 64] }, // Vert
        { days: 3, color: [255, 224, 102] }, // Jaune
        { days: 1, color: [255, 152, 0] }, // Orange
        { days: 0, color: [231, 76, 60] }, // Rouge vif
      ]
      let from = colorStops[0]
      let to = colorStops[colorStops.length - 1]
      for (let i = 0; i < colorStops.length - 1; i++) {
        if (this.daysLeft >= colorStops[i + 1].days) {
          from = colorStops[i]
          to = colorStops[i + 1]
          break
        }
      }
      // Interpolation linéaire
      const range = from.days - to.days
      const pos = Math.max(0, Math.min(1, (from.days - this.daysLeft) / (range || 1)))
      const color = from.color.map((c, i) => Math.round(c + (to.color[i] - c) * pos))
      return `rgb(${color[0]},${color[1]},${color[2]})`
    },
    circleStyle() {
      return {
        width: typeof this.size === 'number' ? this.size + 'px' : this.size,
        height: typeof this.size === 'number' ? this.size + 'px' : this.size,
        background: this.circleColor,
        borderRadius: '50%',
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'center',
        fontWeight: 'bold',
        color: '#fff',
        fontSize: '1.1rem',
        boxShadow: '0 2px 12px rgba(0,0,0,0.13)',
        transition: 'background 0.6s cubic-bezier(.4,2,.6,1)',
        userSelect: 'none',
        border: '3px solid #fff',
        cursor: 'pointer',
      }
    },
  },
}
</script>

<style scoped>
.expiry-circle {
  box-sizing: border-box;
  transition: background 0.6s cubic-bezier(.4,2,.6,1), color 0.3s;
  position: relative;
  overflow: hidden;
  margin: 0 auto;
}
.expiry-date {
  z-index: 2;
  position: relative;
  text-shadow: 0 2px 8px rgba(0,0,0,0.13);
  letter-spacing: 1px;
}
</style>

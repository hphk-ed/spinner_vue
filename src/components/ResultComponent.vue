<template>
  <div class="result-modal-overlay" v-if="winner">
    <div class="result-modal">
      <div class="result-content">
        <h3>결과 발표</h3>
        <div class="winner-info">
          <div class="winner-color" :style="{ backgroundColor: winner.color }"></div>
          <div class="winner-name">{{ winner.name }}</div>
        </div>
        <p class="winner-stats">확률: {{ winner.probability }}%</p>
        <button class="close-button" @click="closeModal">확인</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ResultComponent',
  props: {
    winner: {
      type: Object,
      default: null
    }
  },
  methods: {
    closeModal() {
      this.$emit('close');
    }
  }
}
</script>

<style scoped>
.result-modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease;
}

.result-modal {
  background-color: white;
  border-radius: 12px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.3);
  width: 90%;
  max-width: 400px;
  overflow: hidden;
  animation: scaleIn 0.3s ease;
}

.result-content {
  padding: 25px;
  text-align: center;
}

h3 {
  margin-top: 0;
  margin-bottom: 20px;
  font-size: 24px;
  color: #333;
}

.winner-info {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 20px 0;
}

.winner-color {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  margin-right: 15px;
}

.winner-name {
  font-size: 28px;
  font-weight: bold;
  color: #333;
}

.winner-stats {
  margin: 15px 0 25px;
  color: #666;
  font-size: 16px;
}

.close-button {
  padding: 12px 30px;
  font-size: 16px;
  font-weight: bold;
  border: none;
  border-radius: 5px;
  background-color: #4CAF50;
  color: white;
  cursor: pointer;
  transition: all 0.2s;
}

.close-button:hover {
  background-color: #45a049;
  transform: translateY(-2px);
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes scaleIn {
  from { transform: scale(0.9); opacity: 0; }
  to { transform: scale(1); opacity: 1; }
}
</style>

<template>
  <div class="controls">
    <button @click="onSpin" :disabled="isSpinning" class="spin-button">
      {{ isSpinning ? '돌리는 중...' : '시작' }}
    </button>
  </div>
</template>

<script>
export default {
  name: 'ControlsComponent',
  props: {
    isSpinning: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    onSpin() {
      this.$emit('spin');
    }
  }
}
</script>

<style scoped>
.controls {
  display: flex;
  gap: 10px;
  margin: 20px 0;
}

.spin-button {
  padding: 12px 30px;
  font-size: 18px;
  font-weight: bold;
  border: none;
  border-radius: 5px;
  background-color: #4CAF50;
  color: white;
  cursor: pointer;
  box-shadow: 0 3px 5px rgba(0, 0, 0, 0.2);
  transition: all 0.2s;
}

.spin-button:hover {
  background-color: #45a049;
  transform: translateY(-2px);
}

.spin-button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
  transform: none;
  box-shadow: none;
}
</style>

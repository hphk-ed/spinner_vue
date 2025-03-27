<template>
  <div class="item-form-overlay">
    <div class="item-form">
      <h3>항목 추가</h3>
      <form @submit.prevent="onSubmit">
        <div class="form-group">
          <label for="name">이름:</label>
          <input type="text" id="name" v-model="formData.name" required>
        </div>
        <div class="form-group">
          <label for="count">비율:</label>
          <input type="number" id="count" v-model.number="formData.count" min="1" required>
        </div>
        <div class="form-buttons">
          <button type="submit">추가</button>
          <button type="button" @click="onClose">취소</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AddItemFormComponent',
  data() {
    return {
      formData: {
        name: '',
        count: 1
      }
    }
  },
  methods: {
    onSubmit() {
      this.$emit('add-item', { ...this.formData });
      this.formData = { name: '', count: 1 };
    },
    onClose() {
      this.$emit('close');
    }
  }
}
</script>

<style scoped>
.item-form-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100;
}

.item-form {
  width: 100%;
  max-width: 400px;
  padding: 25px;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.item-form h3 {
  margin-top: 0;
  margin-bottom: 20px;
  font-size: 20px;
  color: #333;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: 500;
}

.form-group input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.form-buttons {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
}

.form-buttons button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 500;
}

.form-buttons button[type="submit"] {
  background-color: #4CAF50;
  color: white;
}

.form-buttons button[type="button"] {
  background-color: #f44336;
  color: white;
}
</style>

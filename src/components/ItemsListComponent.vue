<template>
  <div class="items-list">
    <div v-for="(item, index) in items" :key="index" class="item-row">
      <div class="item-number">{{ index + 1 }}</div>
      <div class="item-color" :style="{ backgroundColor: item.color }"></div>
      <div class="item-name">{{ item.name }}</div>
      <div class="item-count">
        <span>{{ item.count }}</span>
        <div class="cnt-btn">
          <button @click="updateCount(index, 1)">+</button>
          <button @click="updateCount(index, -1)" :disabled="item.count <= 1">-</button>
        </div>
      </div>
      <div class="item-probability">{{ item.probability }}%</div>
      <button class="delete-button" @click="deleteItem(index)">삭제</button>
    </div>
    
    <div class="add-item-row" @click="$emit('add-item')">
      <div class="plus-icon">+</div>
      <div>항목 추가</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ItemsListComponent',
  props: {
    items: {
      type: Array,
      required: true
    }
  },
  methods: {
    updateCount(index, change) {
      this.$emit('update-count', { index, change });
    },
    deleteItem(index) {
      this.$emit('delete-item', index);
    }
  }
}
</script>

<style scoped>
.items-list {
  width: 100%;
  margin-top: 30px;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  background-color: white;
}

.item-row {
  display: flex;
  align-items: center;
  padding: 12px 15px;
  border-bottom: 1px solid #eee;
}

.item-number {
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  background-color: #f2f2f2;
  margin-right: 10px;
  font-weight: bold;
}

.item-color {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  margin-right: 10px;
}

.item-name {
  flex: 4; /* 1에서 2로 변경하여 이름 영역 확장 */
  font-weight: 500;
  overflow: hidden;
  /* text-overflow: ellipsis; */
  white-space: nowrap;
  padding-right: 10px;
}


.item-count {
  display: flex;
  align-items: center;
  margin: 0 15px;
}

.item-count button {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 12px; /* 25px에서 축소 */
  height: 12px; /* 25px에서 축소 */
  border: none;
  background-color: #f0f0f0;
  cursor: pointer;
  font-size: 12px; /* 16px에서 축소 */
  border-radius: 4px;
  text-align: center;
}

.cnt-btn {
  display: flex;
  flex-direction: column;
}

.item-count span {
  margin: 0 10px;
  min-width: 20px;
  text-align: center;
}

.item-probability {
  font-weight: bold;
  width: 60px;
  text-align: right;
  margin-right: 10px;
}

.delete-button {
  padding: 4px 8px; /* 5px 10px에서 축소 */
  background-color: #f44336;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px; /* 글자 크기 축소 */
}

.add-item-row {
  display: flex;
  align-items: center;
  padding: 12px 15px;
  background-color: #f9f9f9;
  cursor: pointer;
  transition: background-color 0.2s;
}

.add-item-row:hover {
  background-color: #f0f0f0;
}

.plus-icon {
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  background-color: #e0e0e0;
  margin-right: 10px;
  font-weight: bold;
  font-size: 20px;
}
</style>

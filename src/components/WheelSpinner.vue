<template>
  <div class="wheel-container">
    <h1 class="title">{{ title }}</h1>
    
    <div class="split-layout">
      <!-- 왼쪽 영역 (돌림판) - 70% -->
      <div class="wheel-section">
        <WheelComponent 
          :items="items" 
          :rotation="rotation" 
          :sliceAngles="sliceAngles" 
          ref="wheel" 
        />
        
        <ControlsComponent 
          :isSpinning="isSpinning" 
          @spin="spinWheel" 
        />
      </div>
      
      <!-- 오른쪽 영역 (아이템 리스트) - 30% -->
      <div class="items-section">
        <ItemsListComponent 
          :items="items" 
          @add-item="showItemForm = true" 
          @update-count="updateItemCount"
          @delete-item="deleteItem"
        />
      </div>
    </div>
    
    <ResultComponent 
      :winner="winner" 
      @close="closeResult" 
    />
    
    <AddItemFormComponent 
      v-if="showItemForm" 
      @add-item="addItem" 
      @close="showItemForm = false" 
    />
  </div>
</template>

<script>
import WheelComponent from './WheelComponent.vue'
import ControlsComponent from './ControlsComponent.vue'
import ResultComponent from './ResultComponent.vue'
import ItemsListComponent from './ItemsListComponent.vue'
import AddItemFormComponent from './AddItemFormComponent.vue'

export default {
  name: 'WheelSpinner',
  components: {
    WheelComponent,
    ControlsComponent,
    ResultComponent,
    ItemsListComponent,
    AddItemFormComponent
  },
  data() {
    return {
      title: '행운의 돌림판',
      items: JSON.parse(localStorage.getItem('wheelItems')) || [
        { name: '피자', count: 4, probability: 15.4, color: '#45ace9' },
        { name: '치킨', count: 10, probability: 38.5, color: '#b8d766' },
        { name: '탕수육', count: 4, probability: 15.4, color: '#ff9f43' },
        { name: '짜장면', count: 4, probability: 15.4, color: '#778ca3' },
        { name: '족발', count: 4, probability: 15.4, color: '#a55eea' }
      ],
      rotation: 0,
      isSpinning: false,
      winner: null,
      showItemForm: false,
      newItem: {
        name: '',
        count: 1
      }
    }
  },
  computed: {
    // 각 항목의 시작 각도와 끝 각도 계산
    sliceAngles() {
      let startAngle = 0;
      const angles = [];
      
      for (const item of this.items) {
        const sliceAngle = (item.probability / 100) * 360;
        const endAngle = startAngle + sliceAngle;
        
        angles.push({
          start: startAngle,
          end: endAngle
        });
        
        startAngle = endAngle;
      }
      
      return angles;
    }
  },
  methods: {
    updateItemCount({ index, change }) {
      this.items[index].count += change;
      this.recalculateProbabilities();
      this.saveItemsToLocalStorage();
    },
    deleteItem(index) {
      this.items.splice(index, 1);
      this.recalculateProbabilities();
      this.saveItemsToLocalStorage();
    },
    getRandomColor() {
      const colors = ['#45ace9', '#b8d766', '#ff9f43', '#778ca3', '#a55eea', '#0abde3', '#eb4d4b', '#20bf6b'];
      return colors[Math.floor(Math.random() * colors.length)];
    },
    spinWheel() {
      if (this.isSpinning) return;
      
      this.isSpinning = true;
      this.winner = null;
      
      // 랜덤하게 회전할 각도 결정 (최소 5바퀴 + 추가 랜덤 각도)
      const spinAngle = 1800 + Math.floor(Math.random() * 360);
      const finalRotation = this.rotation + spinAngle;
      
      // 회전 애니메이션 적용
      this.$refs.wheel.$refs.wheel.style.transition = 'transform 5s cubic-bezier(0.17, 0.67, 0.12, 0.99)';
      this.rotation = finalRotation;
      
      // 회전 완료 후 결과 계산
      setTimeout(() => {
        // 현재 회전 각도 계산 (0-360 범위로 정규화)
        const currentRotation = finalRotation % 360;
        
        // 화살표 위치는 상단 중앙 (0도)
        // 화살표 기준으로 어떤 슬라이스가 선택되었는지 계산
        // 시계 방향으로 회전하므로 반대 방향으로 계산
        const selectedAngle = (360 - currentRotation) % 360;
        
        // 선택된 각도에 해당하는 항목 찾기
        let winnerIndex = -1;
        for (let i = 0; i < this.sliceAngles.length; i++) {
          if (selectedAngle >= this.sliceAngles[i].start && selectedAngle < this.sliceAngles[i].end) {
            winnerIndex = i;
            break;
          }
        }
        
        // 마지막 항목 처리 (360도 경계)
        if (winnerIndex === -1) {
          winnerIndex = this.items.length - 1;
        }
        
        this.isSpinning = false;
        this.winner = this.items[winnerIndex];
        this.$refs.wheel.$refs.wheel.style.transition = 'none';
        
        console.log('Selected angle:', selectedAngle);
        console.log('Winner index:', winnerIndex);
        console.log('Winner:', this.winner.name);
      }, 5000);
    },
    closeResult() {
      this.winner = null;
    },
    addItem(newItem) {
      // 새 항목 추가
      const item = {
        name: newItem.name,
        count: newItem.count,
        color: this.getRandomColor(),
        probability: 0 // 임시값, 아래에서 재계산
      };
      
      this.items.push(item);
      
      // 확률 재계산
      this.recalculateProbabilities();

      this.saveItemsToLocalStorage();
      // 폼 닫기
      this.showItemForm = false;
    },
    saveItemsToLocalStorage() {
      localStorage.setItem('wheelItems', JSON.stringify(this.items));
    },
    recalculateProbabilities() {
      const totalCount = this.items.reduce((sum, item) => sum + item.count, 0);
      
      this.items.forEach(item => {
        item.probability = parseFloat(((item.count / totalCount) * 100).toFixed(1));
      });
      this.saveItemsToLocalStorage();
    }
  },
  mounted() {
    if (!localStorage.getItem('wheelItems')) {
      this.saveItemsToLocalStorage();
    }
  }
}
</script>

<style scoped>
.wheel-container {
  display: flex;
  flex-direction: column;
  max-width: 1800px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Noto Sans KR', sans-serif;
  justify-content: space-between;
}

.title {
  margin-bottom: 20px;
  text-align: center;
  font-size: 28px;
  font-weight: bold;
  color: #333;
}

/* 7:3 분할 레이아웃 */
.split-layout {
  display: flex;
  width: 100%;
  gap: 20px;
  align-items: space-between;
}

/* 왼쪽 영역 - 돌림판 (70%) */
.wheel-section {
  flex: 6;
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* 오른쪽 영역 - 아이템 리스트 (30%) */
.items-section {
  flex: 4;
}

/* 반응형 스타일 */
@media (max-width: 1024px) {
  .split-layout {
    flex-direction: column;
  }
  
  .wheel-section, .items-section {
    flex: auto;
    width: 100%;
  }
}
</style>

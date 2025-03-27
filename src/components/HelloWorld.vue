<template>
  <div class="wheel-container">
    <h1 class="title">{{ title }}</h1>
    
    <div class="wheel-wrapper">
      <div class="wheel" ref="wheel" :style="{ transform: `rotate(${rotation}deg)` }">
        <div 
          v-for="(item, index) in items" 
          :key="index" 
          class="slice" 
          :style="getSliceStyle(index)"
        >
          <div class="slice-content" :style="getContentStyle(index)">
            {{ item.name }}
          </div>
        </div>
      </div>
      
      <div class="pointer">
        <div class="pointer-triangle"></div>
      </div>
    </div>
    
    <div class="controls">
      <button @click="spinWheel" :disabled="isSpinning" class="spin-button">
        {{ isSpinning ? '돌리는 중...' : '시작' }}
      </button>
    </div>
    
    <div class="result" v-if="winner">
      결과: <strong>{{ winner.name }}</strong>
    </div>
    
    <div class="items-list">
      <div v-for="(item, index) in items" :key="index" class="item-row">
        <div class="item-number">{{ index + 1 }}</div>
        <div class="item-color" :style="{ backgroundColor: item.color }"></div>
        <div class="item-name">{{ item.name }}</div>
        <div class="item-count">x{{ item.count }}</div>
        <div class="item-probability">{{ item.probability }}%</div>
      </div>
      
      <div class="add-item-row" @click="showItemForm = true">
        <div class="plus-icon">+</div>
        <div>항목 추가</div>
      </div>
    </div>
    
    <div class="item-form-overlay" v-if="showItemForm">
      <div class="item-form">
        <h3>항목 추가</h3>
        <form @submit.prevent="addItem">
          <div class="form-group">
            <label for="name">이름:</label>
            <input type="text" id="name" v-model="newItem.name" required>
          </div>
          <div class="form-group">
            <label for="count">횟수:</label>
            <input type="number" id="count" v-model.number="newItem.count" min="1" required>
          </div>
          <div class="form-buttons">
            <button type="submit">추가</button>
            <button type="button" @click="showItemForm = false">취소</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'WheelSpinner',
  data() {
    return {
      title: '행운의 돌림판',
      items: [
        { name: '피자', count: 5, probability: 16.7, color: '#45ace9' },
        { name: '치킨', count: 10, probability: 33.3, color: '#b8d766' },
        { name: '탕수육', count: 4, probability: 13.3, color: '#ff9f43' },
        { name: '짜장면', count: 5, probability: 16.7, color: '#778ca3' },
        { name: '족발', count: 4, probability: 13.3, color: '#a55eea' },
        { name: '햄버거', count: 2, probability: 6.7, color: '#0abde3' }
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
    },
    
  },
  methods: {
    getRandomColor() {
      const colors = ['#45ace9', '#b8d766', '#ff9f43', '#778ca3', '#a55eea', '#0abde3', '#eb4d4b', '#20bf6b'];
      return colors[Math.floor(Math.random() * colors.length)];
    },
    getSliceStyle(index) {
      const angle = this.sliceAngles[index];
      const startAngle = angle.start;
      const endAngle = angle.end;
      const largeArcFlag = endAngle - startAngle <= 180 ? 0 : 1;
      
      // 원의 반지름
      const radius = 150;
      
      // 시작점 (원의 중심)
      const x0 = 150;
      const y0 = 150;
      
      // 시작 각도의 좌표
      const x1 = x0 + radius * Math.cos((startAngle - 90) * Math.PI / 180);
      const y1 = y0 + radius * Math.sin((startAngle - 90) * Math.PI / 180);
      
      // 끝 각도의 좌표
      const x2 = x0 + radius * Math.cos((endAngle - 90) * Math.PI / 180);
      const y2 = y0 + radius * Math.sin((endAngle - 90) * Math.PI / 180);
      
      // SVG path 생성
      const path = `M ${x0},${y0} L ${x1},${y1} A ${radius},${radius} 0 ${largeArcFlag},1 ${x2},${y2} Z`;
      
      return {
        position: 'absolute',
        width: '100%',
        height: '100%',
        clipPath: `path('${path}')`,
        backgroundColor: this.items[index].color
      };
    },
    getContentStyle(index) {
      const angle = this.sliceAngles[index];
      const midAngle = (angle.start + angle.end) / 2;
      
      // 텍스트 위치 계산 (반지름의 70% 위치에 배치)
      const radius = 105;
      const x = 150 + radius * Math.cos((midAngle - 90) * Math.PI / 180);
      const y = 150 + radius * Math.sin((midAngle - 90) * Math.PI / 180);
      
      return {
        position: 'absolute',
        left: `${x}px`,
        top: `${y}px`,
        transform: `translate(-50%, -50%) rotate(${midAngle}deg)`,
        fontWeight: 'bold',
        fontSize: '18px',
        color: 'white',
        textShadow: '1px 1px 2px rgba(0, 0, 0, 0.7)',
        width: '60px',
        textAlign: 'center'
      };
    },
    spinWheel() {
      if (this.isSpinning) return;
      
      this.isSpinning = true;
      this.winner = null;
      
      // 랜덤하게 회전할 각도 결정 (최소 5바퀴 + 추가 랜덤 각도)
      const spinAngle = 1800 + Math.floor(Math.random() * 360);
      const finalRotation = this.rotation + spinAngle;
      
      // 회전 애니메이션 적용
      this.$refs.wheel.style.transition = 'transform 5s cubic-bezier(0.17, 0.67, 0.12, 0.99)';
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
        this.$refs.wheel.style.transition = 'none';
        
        console.log('Selected angle:', selectedAngle);
        console.log('Winner index:', winnerIndex);
        console.log('Winner:', this.winner.name);
      }, 5000);
    },

    // 당첨 항목을 찾는 메서드
    findWinningItem(angle) {
      const normalizedAngle = (angle + 360) % 360;
      for (let i = 0; i < this.sliceAngles.length; i++) {
        if (normalizedAngle >= this.sliceAngles[i].start && normalizedAngle < this.sliceAngles[i].end) {
          return this.items[i];
        }
      }
      // 예외 처리: 마지막 항목의 끝 각도가 360도일 경우
      return this.items[this.items.length - 1];
    },


    addItem() {
      // 새 항목 추가
      const newItem = {
        name: this.newItem.name,
        count: this.newItem.count,
        color: this.getRandomColor(),
        probability: 0 // 임시값, 아래에서 재계산
      };
      
      this.items.push(newItem);
      
      // 확률 재계산
      this.recalculateProbabilities();
      
      // 폼 초기화
      this.newItem = { name: '', count: 1 };
      this.showItemForm = false;
    },
    removeItem(index) {
      this.items.splice(index, 1);
      this.recalculateProbabilities();
    },
    recalculateProbabilities() {
      const totalCount = this.items.reduce((sum, item) => sum + item.count, 0);
      
      this.items.forEach(item => {
        item.probability = parseFloat(((item.count / totalCount) * 100).toFixed(1));
      });
    }
  }
}
</script>

<style scoped>
.wheel-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Noto Sans KR', sans-serif;
}

.title {
  margin-bottom: 20px;
  text-align: center;
  font-size: 28px;
  font-weight: bold;
  color: #333;
}

.wheel-wrapper {
  position: relative;
  width: 300px;
  height: 300px;
  margin: 40px 0;
}

.wheel {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  overflow: hidden;
  border: 5px solid #333;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
}

.slice {
  position: absolute;
  width: 100%;
  height: 100%;
}

.pointer {
  position: absolute;
  top: -30px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 10;
}

.pointer-triangle {
  width: 0;
  height: 0;
  border-left: 15px solid transparent;
  border-right: 15px solid transparent;
  border-top: 30px solid #333;
}

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

.result {
  margin: 20px 0;
  padding: 15px 25px;
  background-color: #f0f0f0;
  border-radius: 5px;
  font-size: 20px;
  text-align: center;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.items-list {
  width: 100%;
  margin-top: 30px;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.item-row {
  display: flex;
  align-items: center;
  padding: 12px 15px;
  background-color: white;
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
  flex: 1;
  font-weight: 500;
}

.item-count {
  margin: 0 15px;
  color: #666;
}

.item-probability {
  font-weight: bold;
  width: 60px;
  text-align: right;
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

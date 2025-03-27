<template>
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
</template>

<script>
export default {
  name: 'WheelComponent',
  props: {
    items: {
      type: Array,
      required: true
    },
    rotation: {
      type: Number,
      default: 0
    },
    sliceAngles: {
      type: Array,
      required: true
    }
  },
  methods: {
    getSliceStyle(index) {
      const angle = this.sliceAngles[index];
      const startAngle = angle.start;
      const endAngle = angle.end;
      const largeArcFlag = endAngle - startAngle <= 180 ? 0 : 1;
      
      // 원의 반지름
      const radius = 250; // 크기 증가
      
      // 시작점 (원의 중심)
      const x0 = 250; // 크기 증가
      const y0 = 250; // 크기 증가
      
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
      const radius = 175; // 크기 증가
      const x = 250 + radius * Math.cos((midAngle - 90) * Math.PI / 180); // 크기 증가
      const y = 250 + radius * Math.sin((midAngle - 90) * Math.PI / 180); // 크기 증가
      
      return {
        position: 'absolute',
        left: `${x}px`,
        top: `${y}px`,
        transform: `translate(-50%, -50%) rotate(${midAngle}deg)`,
        fontWeight: 'bold',
        fontSize: '24px', // 폰트 크기 증가
        color: 'white',
        textShadow: '2px 2px 3px rgba(0, 0, 0, 0.7)',
        width: '80px', // 너비 증가
        textAlign: 'center'
      };
    }
  }
}
</script>

<style scoped>
.wheel-wrapper {
  position: relative;
  width: 500px; /* 크기 증가 */
  height: 500px; /* 크기 증가 */
  margin: 40px 0;
}

.wheel {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  overflow: hidden;
  border: 8px solid #333; /* 테두리 두께 증가 */
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.3);
}

.slice {
  position: absolute;
  width: 100%;
  height: 100%;
}

.pointer {
  position: absolute;
  top: -40px; /* 크기에 맞게 조정 */
  left: 50%;
  transform: translateX(-50%);
  z-index: 10;
}

.pointer-triangle {
  width: 0;
  height: 0;
  border-left: 20px solid transparent; /* 크기 증가 */
  border-right: 20px solid transparent; /* 크기 증가 */
  border-top: 40px solid #333; /* 크기 증가 */
}
</style>

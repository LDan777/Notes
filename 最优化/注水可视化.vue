<template>
  <div class="water-filling-demo">
    <h3>注水算法 (Water-filling) 动态演示</h3>
    
    <div class="controls">
      <div class="control-group">
        <label>总水量 (W): {{ totalWater.toFixed(2) }}</label>
        <input type="range" v-model.number="totalWater" min="0.1" max="5.0" step="0.1" />
      </div>
      
      <div class="control-group" v-for="(alpha, index) in alphas" :key="'alpha-'+index">
        <label>通道 {{ index + 1 }} 底噪 (α): {{ alpha.toFixed(2) }}</label>
        <input type="range" v-model.number="alphas[index]" min="0.0" max="2.0" step="0.1" />
      </div>
    </div>

    <div class="results">
      <p>当前统一水位线 (L = 1/ν*): <strong>{{ waterLevel.toFixed(3) }}</strong></p>
    </div>

    <div class="chart-container">
      <div class="water-level-line" :style="{ bottom: waterLevel * scaleFactor + 'px' }"></div>
      
      <div class="bars">
        <div class="bar-wrapper" v-for="(alpha, index) in alphas" :key="'bar-'+index">
          <div class="bar water" 
               :style="{ height: allocations[index] * scaleFactor + 'px' }"
               :title="'分配水量: ' + allocations[index].toFixed(2)">
            {{ allocations[index] > 0 ? allocations[index].toFixed(2) : '' }}
          </div>
          <div class="bar noise" 
               :style="{ height: alpha * scaleFactor + 'px' }"
               :title="'底噪: ' + alpha.toFixed(2)">
            α:{{ alpha.toFixed(1) }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// 1. 初始化响应式数据
const totalWater = ref(1.0)
const alphas = ref([0.2, 0.5, 0.1, 0.8, 0.4])

// 视觉放缩比例 (1单位高度对应多少像素)
const scaleFactor = 150 

// 2. 核心算法：使用二分法计算当前水位的闭包函数
const waterLevel = computed(() => {
  const W = totalWater.value
  const A = alphas.value
  
  let low = 0
  // 水位的理论上限：最高底噪 + 全部水量都倒在同一个池子里
  let high = Math.max(...A) + W 
  let L = 0
  
  // 二分查找精度
  const epsilon = 1e-4 
  
  while (high - low > epsilon) {
    let mid = (low + high) / 2
    // 计算在当前测试水位 mid 下，总共需要多少水
    let currentWaterNeeded = A.reduce((sum, alpha) => sum + Math.max(0, mid - alpha), 0)
    
    if (currentWaterNeeded < W) {
      low = mid // 水不够，需要抬高水位
    } else {
      high = mid // 水太多了，需要降低水位
    }
    L = mid
  }
  return L
})

// 3. 计算每个通道的具体分配量 (x_i)
const allocations = computed(() => {
  const L = waterLevel.value
  return alphas.value.map(alpha => Math.max(0, L - alpha))
})
</script>

<style scoped>
.water-filling-demo {
  font-family: sans-serif;
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

.controls {
  background: #f8f9fa;
  padding: 15px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.control-group {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}

.chart-container {
  position: relative;
  height: 400px;
  border-bottom: 2px solid #333;
  border-left: 2px solid #333;
  margin-top: 30px;
  background: #fafafa;
}

.water-level-line {
  position: absolute;
  left: 0;
  width: 100%;
  border-top: 2px dashed #007bff;
  z-index: 10;
  transition: bottom 0.2s ease;
}

.bars {
  display: flex;
  justify-content: space-around;
  align-items: flex-end;
  height: 100%;
  padding: 0 10px;
}

.bar-wrapper {
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  width: 15%;
}

.bar {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 12px;
  transition: height 0.2s ease;
  overflow: hidden;
}

.water {
  background-color: #4da3ff;
  border-top-left-radius: 4px;
  border-top-right-radius: 4px;
}

.noise {
  background-color: #8c8c8c;
}
</style>

<template>
  <div class="progress-container">
    <div class="progress-info">
      <span class="label">Progresso Geral</span>
      <span class="percentage">{{ porcentagem }}%</span>
    </div>
    <div class="progress-track">
      <div 
        class="progress-fill" 
        :style="{ width: `${porcentagem}%` }"
      ></div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  total: Number,
  concluidas: Number
})

const porcentagem = computed(() => {
  if (props.total === 0) return 0
  return Math.round((props.concluidas / props.total) * 100)
})
</script>

<style scoped>
.progress-container {
  margin-bottom: 32px;
}

.progress-info {
  display: flex;
  justify-content: space-between;
  margin-bottom: 8px;
  font-size: 0.85rem;
  font-weight: 600;
  color: #94a3b8;
}

.percentage {
  color: #10b981;
}

.progress-track {
  height: 8px;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 4px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #10b981, #34d399);
  transition: width 0.5s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: 0 0 10px rgba(16, 185, 129, 0.3);
}
</style>
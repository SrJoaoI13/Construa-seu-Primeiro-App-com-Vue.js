<template>
  <div class="filters-container">
    <div class="search-bar">
      <span class="search-icon">🔍</span>
      <input
        :value="busca"
        @input="$emit('update:busca', $event.target.value)"
        placeholder="Buscar tarefas..."
        class="search-input"
      />
    </div>

    <div class="controls-row">
      <nav class="filters">
        <button
          v-for="f in ['todas', 'pendentes', 'concluidas']"
          :key="f"
          @click="$emit('update:filtro', f)"
          :class="['btn-filter', { active: filtro === f }]"
        >
          {{ f === 'todas' ? '📋 Todas' : f === 'pendentes' ? '⏳ Pendentes' : '✅ Concluídas' }}
        </button>
      </nav>

      <div class="sort-selector">
        <select :value="ordenacao" @change="$emit('update:ordenacao', $event.target.value)">
          <option value="recentes">Mais recentes</option>
          <option value="prioridade">Maior prioridade</option>
          <option value="alfabetica">A-Z</option>
        </select>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  filtro: String,
  busca: String,
  ordenacao: String
})

defineEmits(['update:filtro', 'update:busca', 'update:ordenacao'])
</script>

<style scoped>
.filters-container {
  margin-bottom: 24px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.search-bar {
  position: relative;
  width: 100%;
}

.search-icon {
  position: absolute;
  left: 14px;
  top: 50%;
  transform: translateY(-50%);
  opacity: 0.5;
  font-size: 0.9rem;
}

.search-input {
  width: 100%;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 12px 12px 12px 40px;
  color: #fff;
  font-size: 0.9rem;
  outline: none;
  transition: all 0.3s;
}

.search-input:focus {
  background: rgba(255, 255, 255, 0.08);
  border-color: rgba(255, 255, 255, 0.2);
}

.controls-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 12px;
}

.filters {
  display: flex;
  gap: 8px;
  flex: 1;
}

.btn-filter {
  flex: 1;
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.1);
  padding: 8px;
  border-radius: 10px;
  color: #94a3b8;
  font-size: 0.8rem;
  cursor: pointer;
  transition: all 0.3s;
  white-space: nowrap;
}

.btn-filter.active {
  background: rgba(255, 255, 255, 0.1);
  color: #fff;
  border-color: rgba(255, 255, 255, 0.2);
}

.sort-selector select {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 10px;
  padding: 8px 12px;
  color: #fff;
  font-size: 0.8rem;
  outline: none;
  cursor: pointer;
}

.sort-selector select option {
  background: #1e1e2e;
}
</style>
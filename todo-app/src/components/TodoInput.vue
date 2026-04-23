<template>
  <div class="input-section">
    <div class="input-group">
      <input
        v-model="texto"
        @keyup.enter="handleAdicionar"
        placeholder="O que precisa ser feito?"
        class="main-input"
      />
      <div class="priority-input-wrapper">
        <label>Prioridade (1-5):</label>
        <input
          v-model.number="prioridade"
          type="number"
          min="1"
          max="5"
          class="priority-input"
        />
      </div>
    </div>
    
    <div class="options-row">
      <div class="category-selector">
        <button
          v-for="cat in categorias"
          :key="cat"
          @click="categoriaSelecionada = cat"
          :class="['btn-cat', { active: categoriaSelecionada === cat }]"
        >
          {{ cat }}
        </button>
      </div>
      <button @click="handleAdicionar" class="btn-add">
        <span>+</span> Adicionar
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  categorias: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(['adicionar'])

const texto = ref('')
const prioridade = ref(1)
const categoriaSelecionada = ref(props.categorias[0])

function handleAdicionar() {
  if (!texto.value.trim()) return
  
  emit('adicionar', {
    texto: texto.value.trim(),
    prioridade: Math.min(Math.max(prioridade.value, 1), 5),
    categoria: categoriaSelecionada.value
  })
  
  texto.value = ''
  prioridade.value = 1
}
</script>

<style scoped>
.input-section {
  background: rgba(255, 255, 255, 0.03);
  padding: 20px;
  border-radius: 16px;
  margin-bottom: 32px;
  border: 1px solid rgba(255, 255, 255, 0.05);
}

.input-group {
  display: flex;
  gap: 12px;
  margin-bottom: 16px;
}

.main-input {
  flex: 1;
  background: rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 14px 18px;
  color: #fff;
  font-size: 1rem;
  outline: none;
  transition: all 0.3s;
}

.main-input:focus {
  border-color: #3b82f6;
  background: rgba(0, 0, 0, 0.3);
  box-shadow: 0 0 0 4px rgba(59, 130, 246, 0.1);
}

.priority-input-wrapper {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.priority-input-wrapper label {
  font-size: 0.7rem;
  color: #94a3b8;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.priority-input {
  width: 70px;
  background: rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 10px;
  color: #fff;
  text-align: center;
  outline: none;
}

.options-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 16px;
  min-height: 56px; /* Aumentado para garantir espaço */
}

.category-selector {
  display: flex;
  align-items: center;
  gap: 8px;
  overflow-x: auto;
  padding: 8px 0 12px 0; /* Aumentado padding superior e inferior */
  flex: 1;
  min-width: 0;
  scrollbar-width: thin;
  scrollbar-color: rgba(255, 255, 255, 0.2) transparent;
}

/* Estilização da scrollbar para Chrome/Safari/Edge */
.category-selector::-webkit-scrollbar {
  height: 4px;
}

.category-selector::-webkit-scrollbar-track {
  background: transparent;
}

.category-selector::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 10px;
}

.category-selector::-webkit-scrollbar-thumb:hover {
  background: rgba(255, 255, 255, 0.2);
}

.btn-cat {
  white-space: nowrap;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  padding: 10px 16px; /* Aumentado padding para botões maiores e mais fáceis de clicar */
  border-radius: 20px;
  color: #94a3b8;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.3s;
}

.btn-cat:hover {
  background: rgba(255, 255, 255, 0.1);
}

.btn-cat.active {
  background: #3b82f6;
  color: #fff;
  border-color: #3b82f6;
}

.btn-add {
  background: #3b82f6;
  color: #fff;
  border: none;
  padding: 12px 24px;
  border-radius: 12px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
  display: flex;
  align-items: center;
  gap: 8px;
  flex-shrink: 0; /* Impede que o botão seja espremido */
}

.btn-add:hover {
  background: #2563eb;
  transform: translateY(-2px);
  box-shadow: 0 10px 15px -3px rgba(59, 130, 246, 0.4);
}
</style>
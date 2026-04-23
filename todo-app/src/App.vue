<template>
  <div class="glass-container">
    <header>
      <h1>📝 Minhas Tarefas</h1>
      <p :class="['subtitle', { pulse: animar }]">
        {{ tarefasPendentes }} pendente(s) de {{ tarefas.length }}
      </p>
    </header>

    <ProgressBar 
      :total="tarefas.length" 
      :concluidas="tarefas.length - tarefasPendentes" 
    />

    <TodoInput 
      :categorias="categorias" 
      @adicionar="adicionarTarefa" 
    />

    <TodoFilters 
      v-model:filtro="filtro"
      v-model:busca="busca"
      v-model:ordenacao="ordenacao"
    />

    <main class="todo-list">
      <TransitionGroup name="list">
        <div v-if="tarefasFiltradas.length === 0" key="empty" class="empty-state">
          <span class="icon">{{ busca ? '🔍' : '🎉' }}</span>
          <p>{{ busca ? 'Nenhuma tarefa corresponde à busca.' : 'Nenhuma tarefa encontrada!' }}</p>
        </div>

        <TodoItem
          v-for="tarefa in tarefasFiltradas"
          :key="tarefa.id"
          :tarefa="tarefa"
          @toggle="toggleTarefa"
          @remover="removerTarefa"
          @editar="editarTarefa"
        />
      </TransitionGroup>
    </main>

    <footer v-if="tarefas.some(t => t.feita)">
      <button @click="limparConcluidas" class="btn-clear">
        🧹 Limpar tarefas concluídas
      </button>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import ProgressBar from './components/ProgressBar.vue'
import TodoInput from './components/TodoInput.vue'
import TodoItem from './components/TodoItem.vue'
import TodoFilters from './components/TodoFilters.vue'

const tarefas = ref([])
const filtro = ref('todas')
const busca = ref('')
const ordenacao = ref('recentes')
const animar = ref(false)

const categorias = ['📚 Estudos', '💪 Exercício', '🏠 Casa', '🛒 Compras']

let proximoId = 1

// Carregar do localStorage
onMounted(() => {
  const saved = localStorage.getItem('vue-todo-tasks')
  if (saved) {
    tarefas.value = JSON.parse(saved)
    if (tarefas.value.length > 0) {
      proximoId = Math.max(...tarefas.value.map(t => t.id)) + 1
    }
  } else {
    // Tarefas iniciais se estiver vazio
    tarefas.value = [
      { id: 1, texto: 'Aprender Vue.js com Vite', feita: true, hora: '20:35', categoria: '📚 Estudos', prioridade: 5 },
      { id: 2, texto: 'Implementar Glassmorphism', feita: false, hora: '09:10', categoria: '📚 Estudos', prioridade: 4 },
      { id: 3, texto: 'Organizar o setup', feita: false, hora: '22:00', categoria: '🏠 Casa', prioridade: 2 },
    ]
    proximoId = 4
  }
})

// Salvar no localStorage
watch(tarefas, (newVal) => {
  localStorage.setItem('vue-todo-tasks', JSON.stringify(newVal))
}, { deep: true })

const tarefasPendentes = computed(() =>
  tarefas.value.filter(t => !t.feita).length
)

const tarefasFiltradas = computed(() => {
  let list = tarefas.value

  // Filtro por status
  if (filtro.value === 'pendentes') list = list.filter(t => !t.feita)
  if (filtro.value === 'concluidas') list = list.filter(t => t.feita)
  
  // Filtro por busca
  if (busca.value.trim()) {
    const termo = busca.value.toLowerCase()
    list = list.filter(t => 
      t.texto.toLowerCase().includes(termo) || 
      t.categoria.toLowerCase().includes(termo)
    )
  }

  // Ordenação
  return [...list].sort((a, b) => {
    if (ordenacao.value === 'prioridade') {
      if (b.prioridade !== a.prioridade) return b.prioridade - a.prioridade
      return b.id - a.id
    }
    if (ordenacao.value === 'alfabetica') {
      return a.texto.localeCompare(b.texto)
    }
    // padrão: recentes (id maior primeiro)
    return b.id - a.id
  })
})

function adicionarTarefa({ texto, prioridade, categoria }) {
  const agora = new Date()
  const horaAtual = agora.toLocaleTimeString('pt-BR', {
    hour: '2-digit',
    minute: '2-digit'
  })

  tarefas.value.push({
    id: proximoId++,
    texto,
    feita: false,
    hora: horaAtual,
    categoria,
    prioridade
  })
}

function toggleTarefa(tarefa) {
  tarefa.feita = !tarefa.feita
}

function removerTarefa(id) {
  if (confirm('Tem certeza que deseja remover esta tarefa?')) {
    tarefas.value = tarefas.value.filter(t => t.id !== id)
  }
}

function editarTarefa({ id, texto }) {
  const tarefa = tarefas.value.find(t => t.id === id)
  if (tarefa) {
    tarefa.texto = texto
  }
}

function limparConcluidas() {
  if (confirm('Remover todas as tarefas concluídas?')) {
    tarefas.value = tarefas.value.filter(t => !t.feita)
  }
}

watch(tarefasPendentes, (newVal, oldVal) => {
  if (newVal !== oldVal) {
    animar.value = true
    setTimeout(() => animar.value = false, 300)
  }
})
</script>

<style scoped>
.glass-container {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 24px;
  padding: 40px;
  width: 100%;
  max-width: 650px;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
  color: #fff;
  margin: 2rem 0; /* Adicionado margem para não encostar nas bordas */
}

header {
  text-align: center;
  margin-bottom: 24px;
}

h1 {
  font-size: 2.5rem;
  font-weight: 800;
  background: linear-gradient(to right, #fff, #94a3b8);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  margin-bottom: 8px;
}

.subtitle {
  color: #94a3b8;
  font-size: 0.95rem;
  font-weight: 500;
}

.pulse {
  animation: pulse 0.3s ease-out;
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); color: #60a5fa; }
  100% { transform: scale(1); }
}

.todo-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
  min-height: 200px;
  margin-top: 8px;
}

.empty-state {
  text-align: center;
  padding: 48px 0;
  color: #64748b;
}

.empty-state .icon {
  font-size: 3rem;
  display: block;
  margin-bottom: 16px;
}

footer {
  margin-top: 32px;
  display: flex;
  justify-content: center;
}

.btn-clear {
  background: transparent;
  border: 1px dashed rgba(255, 255, 255, 0.2);
  color: #94a3b8;
  padding: 12px 24px;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s;
  font-size: 0.9rem;
}

.btn-clear:hover {
  border-color: #ef4444;
  color: #ef4444;
  background: rgba(239, 68, 68, 0.05);
}

/* Animations */
.list-enter-active,
.list-leave-active {
  transition: all 0.4s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: scale(0.9);
}
</style>
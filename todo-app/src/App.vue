<template>
  <div class="app">
    <h1>📝 Ver na lista</h1>

    <!-- contador animado -->
    <p :class="['subtitle', { pulse: animar }]">
      {{ tarefasPendentes }} pendente(s) de {{ tarefas.length }}
    </p>

    <!-- Campo para adicionar -->
    <div class="input-area">
      <input
        v-model="novaTarefa"
        @keyup.enter="adicionar"
        placeholder="Digite uma nova tarefa..."
      />
      <button @click="adicionar" class="btn-add">+ Adicionar</button>
    </div>

    <!-- categorias -->
    <div class="categorias">
      <button
        v-for="cat in categorias"
        :key="cat"
        @click="categoriaSelecionada = cat"
        :class="['btn-cat', { ativo: categoriaSelecionada === cat }]"
      >
        {{ cat }}
      </button>
    </div>

    <!-- Filtros -->
    <div class="filtros">
      <button
        v-for="f in ['todas', 'pendentes', 'concluidas']"
        :key="f"
        @click="filtro = f"
        :class="['btn-filtro', { ativo: filtro === f }]"
      >
        {{ f === 'todas' ? '📋 Todas' : f === 'pendentes' ? '⏳ Pendentes' : '✅ Concluídas' }}
      </button>
    </div>

    <!-- Lista de tarefas -->
    <div v-if="tarefasFiltradas.length === 0" class="vazio">
      🎉 Nenhuma tarefa por aqui!
    </div>

    <div
      v-for="tarefa in tarefasFiltradas"
      :key="tarefa.id"
      :class="['tarefa', { concluida: tarefa.feita }]"
    >
      <div class="tarefa-esquerda" @click="tarefa.feita = !tarefa.feita">
        <span class="check">{{ tarefa.feita ? '✅' : '⬜' }}</span>

        <div>
          <span class="tarefa-texto">
            {{ tarefa.categoria }} {{ tarefa.texto }}
          </span>
          <div class="hora">🕒 {{ tarefa.hora }}</div>
        </div>
      </div>

      <button @click="remover(tarefa.id)" class="btn-remover">🗑️</button>
    </div>

    <!-- Limpar concluídas -->
    <button
      v-if="tarefas.some(t => t.feita)"
      @click="limparConcluidas"
      class="btn-limpar"
    >
      🧹 Limpar concluídas
    </button>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const novaTarefa = ref('')
const filtro = ref('todas')
const categoriaSelecionada = ref('📚 Estudos')
const animar = ref(false)

let proximoId = 4

const categorias = ['📚 Estudos', '💪 Exercício', '🏠 Casa']

const tarefas = ref([
  { id: 1, texto: 'Aprender Vue.js', feita: true, hora: '20:35', categoria: '📚 Estudos' },
  { id: 2, texto: 'Criar meu primeiro componente', feita: false, hora: '09:10', categoria: '📚 Estudos' },
  { id: 3, texto: 'Dominar o mundo', feita: false, hora: '22:00', categoria: '🏠 Casa' },
])

const tarefasPendentes = computed(() =>
  tarefas.value.filter(t => !t.feita).length
)

const tarefasFiltradas = computed(() => {
  if (filtro.value === 'pendentes') return tarefas.value.filter(t => !t.feita)
  if (filtro.value === 'concluidas') return tarefas.value.filter(t => t.feita)
  return tarefas.value
})

function adicionar() {
  const texto = novaTarefa.value.trim()
  if (!texto) return

  const horaAtual = new Date().toLocaleTimeString('pt-BR', {
    hour: '2-digit',
    minute: '2-digit'
  })

  tarefas.value.push({
    id: proximoId++,
    texto,
    feita: false,
    hora: horaAtual,
    categoria: categoriaSelecionada.value
  })

  novaTarefa.value = ''
}

function remover(id) {
  tarefas.value = tarefas.value.filter(t => t.id !== id)
}

function limparConcluidas() {
  tarefas.value = tarefas.value.filter(t => !t.feita)
}

watch(tarefasPendentes, () => {
  animar.value = true
  setTimeout(() => animar.value = false, 300)
})
</script>

<style scoped>
.app {
  max-width: 500px;
  margin: 40px auto;
  padding: 30px;
  font-family: 'Segoe UI', sans-serif;
  background: #1a1a2e;
  border-radius: 20px;
  color: #e6e6e6;
}

h1 {
  text-align: center;
  font-size: 28px;
  margin-bottom: 4px;
}

.subtitle {
  text-align: center;
  color: #888;
  font-size: 14px;
  margin-bottom: 24px;
}

.pulse {
  animation: pulse 0.3s ease;
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.2); color: #3b82f6; }
  100% { transform: scale(1); }
}

.input-area {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;
}

input {
  flex: 1;
  padding: 12px 16px;
  border-radius: 10px;
  border: 2px solid #333;
  background: #16213e;
  color: #fff;
  font-size: 14px;
  outline: none;
  transition: border 0.2s;
}

input:focus {
  border-color: #3b82f6;
}

/* NOVA COR BOTÃO PRINCIPAL */
.btn-add {
  padding: 12px 20px;
  background: linear-gradient(135deg, #3b82f6, #2563eb);
  color: white;
  border: none;
  border-radius: 10px;
  font-weight: 600;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.2s;
}

.btn-add:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(59,130,246,0.4);
}

/* categorias */
.categorias {
  display: flex;
  gap: 6px;
  margin-bottom: 16px;
}

.btn-cat {
  flex: 1;
  padding: 6px;
  border-radius: 8px;
  border: 1px solid #333;
  background: transparent;
  color: #888;
  font-size: 12px;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-cat.ativo {
  background: #3b82f6;
  color: white;
  border-color: #3b82f6;
}

.filtros {
  display: flex;
  gap: 6px;
  margin-bottom: 20px;
}

.btn-filtro {
  flex: 1;
  padding: 8px;
  border-radius: 8px;
  border: 1px solid #333;
  background: transparent;
  color: #888;
  font-size: 12px;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-filtro.ativo {
  background: #3b82f6;
  color: white;
  border-color: #3b82f6;
}

.tarefa {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 16px;
  margin-bottom: 8px;
  background: #16213e;
  border-radius: 10px;
  border: 1px solid #333;
  transition: all 0.2s;
}

.tarefa:hover {
  border-color: #3b82f6;
}

.tarefa.concluida {
  opacity: 0.5;
}

.tarefa.concluida .tarefa-texto {
  text-decoration: line-through;
}

.tarefa-esquerda {
  display: flex;
  align-items: center;
  gap: 12px;
  cursor: pointer;
  flex: 1;
}

.check {
  font-size: 18px;
}

.tarefa-texto {
  font-size: 14px;
}

.hora {
  font-size: 11px;
  color: #666;
}

.btn-remover {
  background: none;
  border: none;
  font-size: 16px;
  cursor: pointer;
  opacity: 0.4;
  transition: opacity 0.2s;
}

.btn-remover:hover {
  opacity: 1;
}

.btn-limpar {
  width: 100%;
  padding: 10px;
  margin-top: 16px;
  background: transparent;
  border: 1px dashed #555;
  color: #888;
  border-radius: 10px;
  cursor: pointer;
  font-size: 13px;
  transition: all 0.2s;
}

.btn-limpar:hover {
  border-color: #3b82f6;
  color: #3b82f6;
}

.vazio {
  text-align: center;
  padding: 40px;
  color: #555;
  font-size: 16px;
}
</style>
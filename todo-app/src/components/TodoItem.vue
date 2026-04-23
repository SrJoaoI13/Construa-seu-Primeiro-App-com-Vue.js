<template>
  <div
    :class="['todo-item', { completed: tarefa.feita, [`priority-${tarefa.prioridade}`]: true }]"
  >
    <div class="item-content" @click="$emit('toggle', tarefa)">
      <div class="checkbox">
        <div class="check-inner" v-if="tarefa.feita">L</div>
      </div>
      <div class="text-group">
        <template v-if="!editando">
          <span class="task-text">{{ tarefa.texto }}</span>
        </template>
        <template v-else>
          <input
            ref="inputEdit"
            v-model="textoEdit"
            class="edit-input"
            @keyup.enter="salvarEdicao"
            @blur="salvarEdicao"
            @click.stop
          />
        </template>
        <div class="task-meta">
          <span class="tag">{{ tarefa.categoria }}</span>
          <span class="time">🕒 {{ tarefa.hora }}</span>
          <span class="priority-tag">P{{ tarefa.prioridade }}</span>
        </div>
      </div>
    </div>
    <div class="actions">
      <button @click.stop="iniciarEdicao" class="btn-action" title="Editar">
        ✏️
      </button>
      <button @click.stop="$emit('remover', tarefa.id)" class="btn-action" title="Remover">
        🗑️
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, nextTick } from 'vue'

const props = defineProps({
  tarefa: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['toggle', 'remover', 'editar'])

const editando = ref(false)
const textoEdit = ref('')
const inputEdit = ref(null)

function iniciarEdicao() {
  textoEdit.value = props.tarefa.texto
  editando.value = true
  nextTick(() => {
    inputEdit.value?.focus()
  })
}

function salvarEdicao() {
  if (!editando.value) return
  
  if (textoEdit.value.trim() && textoEdit.value !== props.tarefa.texto) {
    emit('editar', { id: props.tarefa.id, texto: textoEdit.value.trim() })
  }
  editando.value = false
}
</script>

<style scoped>
.todo-item {
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(255, 255, 255, 0.05);
  border-radius: 16px;
  padding: 16px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  transition: all 0.3s;
}

.todo-item:hover {
  background: rgba(255, 255, 255, 0.06);
  border-color: rgba(255, 255, 255, 0.1);
  transform: translateX(4px);
}

.item-content {
  display: flex;
  align-items: center;
  gap: 16px;
  cursor: pointer;
  flex: 1;
}

.checkbox {
  width: 24px;
  height: 24px;
  border: 2px solid rgba(255, 255, 255, 0.2);
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s;
  flex-shrink: 0;
}

.todo-item.completed .checkbox {
  background: #10b981;
  border-color: #10b981;
}

.check-inner {
  color: #fff;
  font-weight: bold;
  font-size: 14px;
  transform: rotate(45deg) scaleX(-1);
  margin-bottom: 2px;
}

.text-group {
  display: flex;
  flex-direction: column;
  gap: 4px;
  flex: 1;
}

.task-text {
  font-size: 1rem;
  font-weight: 500;
  transition: all 0.3s;
  word-break: break-word;
}

.edit-input {
  background: rgba(0, 0, 0, 0.3);
  border: 1px solid #3b82f6;
  border-radius: 8px;
  padding: 4px 8px;
  color: #fff;
  font-size: 1rem;
  width: 100%;
  outline: none;
}

.todo-item.completed .task-text {
  color: #64748b;
  text-decoration: line-through;
}

.task-meta {
  display: flex;
  align-items: center;
  gap: 12px;
  flex-wrap: wrap;
}

.tag {
  font-size: 0.7rem;
  background: rgba(59, 130, 246, 0.1);
  color: #60a5fa;
  padding: 2px 8px;
  border-radius: 6px;
}

.time {
  font-size: 0.7rem;
  color: #64748b;
}

.priority-tag {
  font-size: 0.7rem;
  font-weight: bold;
  padding: 2px 6px;
  border-radius: 4px;
}

/* Priority Colors */
.priority-5 { border-left: 4px solid #ef4444; }
.priority-4 { border-left: 4px solid #f97316; }
.priority-3 { border-left: 4px solid #eab308; }
.priority-2 { border-left: 4px solid #3b82f6; }
.priority-1 { border-left: 4px solid #64748b; }

.priority-5 .priority-tag { background: rgba(239, 68, 68, 0.1); color: #ef4444; }
.priority-4 .priority-tag { background: rgba(249, 115, 22, 0.1); color: #f97316; }
.priority-3 .priority-tag { background: rgba(234, 179, 8, 0.1); color: #eab308; }
.priority-2 .priority-tag { background: rgba(59, 130, 246, 0.1); color: #3b82f6; }
.priority-1 .priority-tag { background: rgba(100, 116, 139, 0.1); color: #64748b; }

.actions {
  display: flex;
  gap: 4px;
  opacity: 0;
  transition: opacity 0.3s;
}

.todo-item:hover .actions {
  opacity: 1;
}

.btn-action {
  background: transparent;
  border: none;
  font-size: 1.1rem;
  cursor: pointer;
  padding: 8px;
  border-radius: 8px;
  transition: all 0.2s;
}

.btn-action:hover {
  background: rgba(255, 255, 255, 0.1);
}
</style>
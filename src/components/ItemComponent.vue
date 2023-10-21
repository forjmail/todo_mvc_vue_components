<template>
  <div class="view">
    <input class="toggle" type="checkbox" v-model="todo.completed" />
    <label @dblclick="editTodo(todo)">{{ todo.title }}</label>
    <button class="destroy" @click="removeTodo(todo.id)"></button>
  </div>
  <input
    v-if="todo === editedTodo"
    class="edit"
    type="text"
    v-model="todo.title"
    @vue:mounted="mountFunc"
    @blur="doneEdit(todo)"
    @keyup.enter="doneEdit(todo)"
    @keyup.escape="cancelEdit(todo)"
  />
</template>

<script setup lang="ts">
import type { Todo } from '@/types'
import { ref } from 'vue'

const props = defineProps<{
  todo: Todo
}>()
const { todo } = props

const emit = defineEmits<{
  setEdit: [editId: number]
  removeTodoEmiter: [deleteId: number]
}>()

let editedTodo = ref<Todo | null>(null)

let beforeEditCache = ''
function editTodo(todo: Todo) {
  beforeEditCache = todo.title
  editedTodo.value = todo
  emit('setEdit', todo.id)
}

function doneEdit(todo: Todo) {
  if (editedTodo.value) {
    todo.title = todo.title.trim()
    editedTodo.value = null
    emit('setEdit', 0)
    beforeEditCache = ''
    if (!todo.title) removeTodo(todo.id)
  }
}

function cancelEdit(todo: Todo) {
  todo.title = beforeEditCache
  editedTodo.value = null
  emit('setEdit', 0)
  beforeEditCache = ''
}

function removeTodo(deleteId: number) {
  emit('removeTodoEmiter', deleteId)
}

function mountFunc() {
  return (el: HTMLInputElement): void => el.focus()
}
</script>

<style scoped></style>

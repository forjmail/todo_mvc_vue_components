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

<style scoped>
.toggle {
  text-align: center;
  width: 40px;
  /* auto, since non-WebKit browsers doesn't support input styling */
  height: auto;
  position: absolute;
  top: 0;
  bottom: 0;
  margin: auto 0;
  border: none; /* Mobile Safari */
  -webkit-appearance: none;
  appearance: none;
  opacity: 0;
}

.toggle + label {
  /*
		Firefox requires `#` to be escaped - https://bugzilla.mozilla.org/show_bug.cgi?id=922433
		IE and Edge requires *everything* to be escaped to render, so we do that instead of just the `#` - https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/7157459/
	*/
  background-image: url('data:image/svg+xml;utf8,%3Csvg%20xmlns%3D%22http%3A//www.w3.org/2000/svg%22%20width%3D%2240%22%20height%3D%2240%22%20viewBox%3D%22-10%20-18%20100%20135%22%3E%3Ccircle%20cx%3D%2250%22%20cy%3D%2250%22%20r%3D%2250%22%20fill%3D%22none%22%20stroke%3D%22%23949494%22%20stroke-width%3D%223%22/%3E%3C/svg%3E');
  background-repeat: no-repeat;
  background-position: center left;
}

.toggle:checked + label {
  background-image: url('data:image/svg+xml;utf8,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%2240%22%20height%3D%2240%22%20viewBox%3D%22-10%20-18%20100%20135%22%3E%3Ccircle%20cx%3D%2250%22%20cy%3D%2250%22%20r%3D%2250%22%20fill%3D%22none%22%20stroke%3D%22%2359A193%22%20stroke-width%3D%223%22%2F%3E%3Cpath%20fill%3D%22%233EA390%22%20d%3D%22M72%2025L42%2071%2027%2056l-4%204%2020%2020%2034-52z%22%2F%3E%3C%2Fsvg%3E');
}

label {
  word-break: break-all;
  padding: 15px 15px 15px 60px;
  display: block;
  line-height: 1.2;
  transition: color 0.4s;
  font-weight: 400;
  color: #484848;
}

.destroy {
  display: none;
  position: absolute;
  top: 0;
  right: 10px;
  bottom: 0;
  width: 40px;
  height: 40px;
  margin: auto 0;
  font-size: 30px;
  color: #949494;
  transition: color 0.2s ease-out;
}

.destroy:hover,
.destroy:focus {
  color: #c18585;
}

.destroy:after {
  content: '×';
  display: block;
  height: 100%;
  line-height: 1.1;
}

.todo-list li:hover .destroy {
  display: block;
}

.edit {
  display: none;
}

.toggle:focus + label {
  box-shadow: 0 0 2px 2px #cf7d7d;
  outline: 0;
}
</style>

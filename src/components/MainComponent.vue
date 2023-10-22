<template>
  <section class="main" v-show="props.todos.length">
    <input id="toggle-all" class="toggle-all" type="checkbox" @change="toggleAll" />
    <label for="toggle-all">Mark all as complete</label>
    <ul class="todo-list">
      <li
        v-for="todo in filteredTodos"
        class="todo"
        :key="todo.id"
        :class="{ completed: todo.completed, editing: todo.id === editId }"
      >
        <ItemComponent
          :todo="todo"
          @removeTodoEmiter="
            (deleteId) => {
              emit('removeTodoEmiter', deleteId)
            }
          "
          @setEdit="editTodo"
        />
      </li>
    </ul>
  </section>
</template>

<script setup lang="ts">
import ItemComponent from './ItemComponent.vue'
import type { Filters, Todo } from '@/types'
import { computed, ref } from 'vue'

const filters: Filters = {
  all: (todos: Todo[]): Todo[] => todos,
  active: (todos: Todo[]): Todo[] => todos.filter((todo: Todo) => !todo.completed),
  completed: (todos: Todo[]): Todo[] => todos.filter((todo: Todo) => todo.completed)
}

const props = defineProps<{
  todos: Todo[]
  visibility: string
}>()

const emit = defineEmits<{
  removeTodoEmiter: [deleteId: number]
}>()

const editId = ref<number>(0)

const filteredTodos = computed(() => filters[props.visibility](props.todos))

function toggleAll(event: Event) {
  const el = event.target as HTMLInputElement
  props.todos.forEach((todo: Todo) => (todo.completed = el.checked))
}

function editTodo(todoId: number) {
  editId.value = todoId
}
</script>

<style scoped>
.main {
  position: relative;
  z-index: 2;
  border-top: 1px solid #e6e6e6;
}

.toggle-all {
  width: 1px;
  height: 1px;
  border: none; /* Mobile Safari */
  opacity: 0;
  position: absolute;
  right: 100%;
  bottom: 100%;
}

.toggle-all + label {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 45px;
  height: 65px;
  font-size: 0;
  position: absolute;
  top: -65px;
  left: -0;
}

.toggle-all + label:before {
  content: '❯';
  display: inline-block;
  font-size: 22px;
  color: #949494;
  padding: 10px 27px 10px 27px;
  -webkit-transform: rotate(90deg);
  transform: rotate(90deg);
}

.toggle-all:checked + label:before {
  color: #484848;
}

/*
	Hack to remove background from Mobile Safari.
	Can't use it globally since it destroys checkboxes in Firefox
*/
@media screen and (-webkit-min-device-pixel-ratio: 0) {
  .toggle-all,
  .todo-list li .toggle {
    background: none;
  }

  .todo-list li .toggle {
    height: 40px;
  }
}

.todo-list {
  margin: 0;
  padding: 0;
  list-style: none;
}

.todo-list li {
  position: relative;
  font-size: 24px;
  border-bottom: 1px solid #ededed;
}

.todo-list li:last-child {
  border-bottom: none;
}

.todo-list li.editing {
  border-bottom: none;
  padding: 0;
}

.todo-list li.editing:last-child {
  margin-bottom: -1px;
}

:global(.todo-list li.editing .edit) {
  display: block;
  width: calc(100% - 43px);
  padding: 12px 16px;
  margin: 0 0 0 43px;
}

:global(.todo-list li.editing .view) {
  display: none;
}

:global(.todo-list li.completed label) {
  color: #949494;
  text-decoration: line-through;
}

.toggle-all:focus + label {
  box-shadow: 0 0 2px 2px #cf7d7d;
  outline: 0;
}
</style>

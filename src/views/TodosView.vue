<script setup lang="ts">
import { uid } from 'uid'
import TodoCreator from '@/components/TodoCreator.vue'
import { computed, ref, watch } from 'vue'
import TodoItem from '@/components/TodoItem.vue'
import { Icon } from '@iconify/vue/dist/iconify.js'

interface Todo {
  id: string
  todo: string
  isCompleted: boolean
  isEditing: boolean
}

const todoList = ref<Todo[]>([])

const fetchTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem('todoList') ?? '[]')
  todoList.value = savedTodoList
}

fetchTodoList()

const setTodoListLocalStorage = () => {
  localStorage.setItem('todoList', JSON.stringify(todoList.value))
}

watch(todoList, setTodoListLocalStorage, {
  deep: true
})

const createTodo = (todo: string) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: false,
    isEditing: false
  })
}

const toggleTodoComplete = (index: number) => {
  todoList.value[index].isCompleted = !todoList.value[index].isCompleted
}

const editTodo = (index: number) => {
  todoList.value[index].isEditing = !todoList.value[index].isEditing
}

const updateTodo = (value: string, index: number) => {
  todoList.value[index].todo = value
}

const deleteTodo = (id: string) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== id)
}

const todoCompleted = computed(() => todoList.value.every((el) => el.isCompleted))
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem
        v-bind:key="index"
        v-for="(todo, index) in todoList"
        :todo="todo"
        :index="index"
        @toggle-complete="toggleTodoComplete"
        @edit-todo="editTodo"
        @update-todo="updateTodo"
        @delete-todo="deleteTodo"
      />
    </ul>
    <p v-else class="todos-msg">
      <Icon icon="noto-v1:sad-but-relieved-face" width="22" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" width="22" />
      <span>You have completed ALL your todo's!</span>
    </p>
  </main>
</template>

<style lang="scss" scoped>
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }
  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>

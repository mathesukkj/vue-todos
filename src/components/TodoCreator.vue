<script setup lang="ts">
import { reactive } from 'vue'
import TodoButton from './TodoButton.vue'

const emit = defineEmits(['create-todo'])

const todoState = reactive({
  todo: '',
  success: false,
  invalid: false,
  errorMsg: '',
  successMsg: 'Todo created successfully!'
})

const createTodo = () => {
  if (todoState.todo !== '') {
    emit('create-todo', todoState.todo)
    todoState.todo = ''
    todoState.success = true
    return
  }

  todoState.invalid = true
  todoState.success = false
  todoState.errorMsg = 'Todo value cannot be empty!'
}

const removeMessage = () => {
  todoState.invalid = false
  todoState.success = false
}
</script>

<template>
  <div class="input-wrap" :class="{ 'input-error': todoState.invalid }">
    <input type="text" @keypress="removeMessage()" v-model="todoState.todo" />
    <TodoButton @click="createTodo()" />
  </div>
  <p v-show="todoState.invalid" class="msg error">{{ todoState.errorMsg }}</p>
  <p v-show="todoState.success" class="msg success">{{ todoState.successMsg }}</p>
</template>

<style lang="scss" scoped>
.msg {
  margin-top: 6px;
  font-size: 12px;
  text-align: center;

  &.error {
    color: red;
  }

  &.success {
    color: #008877;
  }
}

.input-wrap {
  display: flex;
  transition: 250ms ease;
  border: 2px solid #41b080;

  &:focus-within {
    box-shadow:
      0 -4px 6px -1px rgb(0 0 0 / 0.1),
      0 -2px 4px -2px rgb(0 0 0 / 0.1);
  }

  &.input-error {
    border-color: red;
  }

  input {
    width: 100%;
    padding: 8px 6px;
    border: none;

    &:focus {
      outline: none;
    }
  }
}
</style>

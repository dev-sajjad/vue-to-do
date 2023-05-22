<script setup>
import { computed, ref, watch } from "vue";
import { uid } from "uid";
import { Icon } from "@iconify/vue";

import TodoCreator from "../components/TodoCreator.vue";
import TodoItem from "../components/TodoItem.vue";

const todoList = ref([]);

watch(
  todoList,
  () => {
    setTodoListLocalStorage();
  },
  {
    deep: true,
  }
);

//checking if all the todo's have been done
const todoCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted);
});

// fetch todo's from local storage
const fetchTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem("todoList"));
  if (savedTodoList) {
    todoList.value = savedTodoList;
  }
};
// fetch todo's on page load
fetchTodoList();

// set todoList to local storage
const setTodoListLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value));
};

// create a new todo
const createTodo = (todo) => {
  todoList.value.push({
    id: uid(6),
    todo,
    isCompleted: false,
    isEditing: false,
  });
};

const toggleTodoComplete = (index) => {
  todoList.value[index].isCompleted = !todoList.value[index].isCompleted;
};

const toggleEditTodo = (index) => {
  todoList.value[index].isEditing = !todoList.value[index].isEditing;
};

const updateTodo = (newVal, index) => {
  todoList.value[index].todo = newVal;
};

const deleteTodo = (todoId) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoId);
};
</script>

<template>
  <main>
    <h1>Create To-do</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem
        v-for="(todo, index) in todoList"
        :key="todo.id"
        :todo="todo"
        :index="index"
        @toggle-complete="toggleTodoComplete"
        @edit-todo="toggleEditTodo"
        @update-todo="updateTodo"
        @delete-todo="deleteTodo"
      />
    </ul>
    <p v-else class="todos-msg">
      <Icon icon="noto-v1:sad-but-relieved-face" width="24" />
      <span>You have no todo's to complete. Add One!</span>
    </p>
    <p v-if="todoCompleted && todoList.length > 0" class="todo-complete">
      <Icon icon="noto-v1:party-popper" width="24" />
      <span>You have done all your to-do's!</span>
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
    font-size: 1.2rem;
  }
  .todo-complete {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
    font-size: 1.2rem;
  }
}
</style>

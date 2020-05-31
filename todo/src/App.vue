<template>
  <div id="app">
    <todo-list />
    <todo-item />
  </div>
</template>

<script>
import TodoList from './components/TodoList';
import TodoItem from './components/TodoItem';

export default {
  components: {
    TodoList,
    TodoItem,
  },
};
</script>

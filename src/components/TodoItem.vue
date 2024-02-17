<template>
  <div class="todo-item">
    <h3>{{ todo.title }}</h3>
    <p>{{ todo.description }}</p>
    <p>Status: {{ todo.status }}</p>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';

export default defineComponent({
  props: {
    todo: {
      type: Object as PropType<{ title: string; description: string; status: string }>,
      required: true
    }
  }
});
</script>

<style scoped>
.todo-item {
  margin-bottom: 20px;
  border: 1px solid #ccc;
  padding: 10px;
}
</style>

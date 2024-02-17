<template>
  <div class="todo-list">
    <h2>Todo List</h2>
    <div v-if="loading">Loading...</div>
    <div v-else>
      <div v-for="todo in todos" :key="todo.id" class="todo-item">
        <h3>{{ todo.title }}</h3>
        <p>{{ todo.description }}</p>
        <button @click="deleteTodo(todo.id)">Delete</button>
        <button @click="toggleEdit(todo)">Edit</button>
        <div v-if="editingTodo && editingTodo.id === todo.id">
          <h2>Edit Todo</h2>
          <form @submit.prevent="updateTodo">
            <input v-model="editTodoData.title" placeholder="New title" required>
            <input v-model="editTodoData.description" placeholder="New description" required>
            <button type="submit">Update Todo</button>
            <button @click="cancelEdit">Cancel</button>
          </form>
        </div>
      </div>
    </div>
    <form @submit.prevent="addTodo">
      <input v-model="newTodo.title" placeholder="title" required>
      <input v-model="newTodo.description" placeholder="description" required>
      <button type="submit">Add Todo</button>
    </form>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import axios from 'axios';

export default defineComponent({
  setup() {
    const todos = ref([]);
    const loading = ref(true);
    const newTodo = ref({ title: '', description: '' });
    const editingTodo = ref();
    const editTodoData = ref({ title: '', description: '' });

    const fetchTodos = async () => {
      try {
        const response = await axios.get('http://localhost:8080/api/todo');
        todos.value = response.data.todos;
      } catch (error) {
        console.error('Error loading todos:', error);
      } finally {
        loading.value = false;
      }
    };

    const addTodo = async () => {
      try {
        const newTodoData = {
          title: newTodo.value.title,
          description: newTodo.value.description,
        };
        await axios.post('http://localhost:8080/api/todo', newTodoData);
        await fetchTodos();
        newTodo.value = { title: '', description: '' };
      } catch (error) {
        console.error('Error adding todo:', error);
      }
    };

    const deleteTodo = async (id: number) => {
      try {
        await axios.delete(`http://localhost:8080/api/todo/${id}`);
        await fetchTodos();
      } catch (error) {
        console.error('Error deleting todo:', error);
      }
    };

    const toggleEdit = (todo: any) => {
      if (editingTodo.value && editingTodo.value.id === todo.id) {
        editingTodo.value = null;
      } else {
        editingTodo.value = todo;
        editTodoData.value.title = todo.title;
        editTodoData.value.description = todo.description;
      }
    };

    const updateTodo = async () => {
      try {
        const id = editingTodo.value.id;
        const updatedTodoData = {
          title: editTodoData.value.title,
          description: editTodoData.value.description,
        };
        const response = await axios.put(`http://localhost:8080/api/todo/${id}`, updatedTodoData);
        console.log('Todo item edited successfully:', response.data);
        editingTodo.value = null;
        await fetchTodos();
      } catch (error) {
        console.error('Error editing todo:', error);
      }
    };

    const cancelEdit = () => {
      editingTodo.value = null;
    };

    onMounted(fetchTodos);

    return {
      todos,
      loading,
      newTodo,
      editingTodo,
      editTodoData,
      addTodo,
      deleteTodo,
      toggleEdit,
      updateTodo,
      cancelEdit
    };
  }
});
</script>

<style scoped>
.todo-list {
  margin-top: 20px;
}

.todo-item {
  margin-bottom: 20px;
  border: 1px solid #ccc;
  padding: 10px;
}
</style>

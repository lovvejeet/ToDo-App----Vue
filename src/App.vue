<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up,
        <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          placeholder="e.g. make a video"
          v-model="inputContent"
        />
        <h4>Pick a category</h4>
        <div class="options">
          <label
            ><input
              type="radio"
              name="category"
              value="business"
              v-model="inputCategory"
            /><span class="bubble business"> </span>
            <div>Business</div>
          </label>
          <label
            ><input
              type="radio"
              name="category"
              value="personal"
              v-model="inputCategory"
            /><span class="bubble personal"> </span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div
          v-for="(todo, idx) in todosAscending"
          :class="`todo-item ${todo - done && 'done'}`"
          :key="idx"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script>
import { ref, onMounted, computed, watch } from "vue";

const name = ref("");
const todos = ref([]);
const inputContent = ref("");
const inputCategory = ref(null);

//sorting todo  eg: new todo will be on top
const todosAscending = computed(() =>
  todos.value.sort((a, b) => b.createdAt - a.createdAt)
);

//storing name in localStorage
watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);
//pulling values from localStorage to display
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || []);
});

//adding todo
const addTodo = () => {
  if (inputContent.value.trim() === "" || inputCategory.value === null) {
    return;
  }
  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  inputContent.value = "";
  inputCategory.value = null;
};
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};
</script>

<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todo = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);
const todoASC = computed(() =>
  todo.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);


const addTodo = () => {
  if (input_content.value.trim() === "" || input_content.value === null) {
    return;
  }

  todo.value.push({
    id: new Date().getTime(),
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
};

const removeTodo = (todoToRemove) => {
  todo.value = todo.value.filter((e) => e.id !== todoToRemove.id);
};

watch(
  todo,
  (newValue) => {
    localStorage.setItem("todo", JSON.stringify(newValue));
  },
  { deep: true }
);

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  const storedTodo = JSON.parse(localStorage.getItem("todo"));
  todo.value = Array.isArray(storedTodo) ? storedTodo : [];
});
</script>

<template>
  <main class="app p-4 position-relative" style="z-index: 20;">
    <div class="card shadow-sm">
      <section class="greeting">
        <h2 class="text-primary">Welcome to task list</h2>
        <div class="form-floating">
          <input
            v-model="name"
            type="text"
            class="form-control form-control-lg rounded no-focus bg-white shadow-sm align-items-center justify-content-center"
            id="floatingInputGroup1"
            placeholder="Your Name"
          />
          <label for="floatingInputGroup1">Your Name</label>
        </div>
      </section>
      <section class="mt-4">
        <form @submit.prevent="addTodo">
          <p class="text-start">What's on your task list?</p>
          <input
            class="form-control form-control-lg rounded bg-white shadow-sm"
            type="text"
            v-model="input_content"
            placeholder="Do a Homework etc."
          />
          <p class="mt-4 text-start">What's your task list category?</p>
          <div class="d-flex gap-4 mt-4" style="height: 4rem">
            <div
              class="container d-flex flex-row rounded border bg-white shadow-sm p-2 align-items-center justify-content-center"
            >
              <div class="form-check">
                <input
                  class="form-check-input"
                  type="radio"
                  name="category"
                  id="category_personal"
                  value="Personal"
                  v-model="input_category"
                />
                <label class="form-check-label" for="flexRadioDefault1">
                  Personal
                </label>
              </div>
            </div>
            <div
              class="container d-flex rounded border bg-white shadow-sm p-2 align-items-center justify-content-center"
            >
              <div class="form-check">
                <input
                  class="form-check-input"
                  type="radio"
                  name="category"
                  id="category_work"
                  value="Work"
                  v-model="input_category"
                />
                <label class="form-check-label" for="flexRadioDefault1">
                  Work
                </label>
              </div>
            </div>
          </div>
          <button
            type="submit"
            class="btn btn-primary mt-4 btn-block"
            value="Add Task"
            style="width: 100%"
          >
            Add Task
          </button>
        </form>
      </section>
    </div>
    <section>
      <p class="text-start mt-4">Todo List</p>
      <div
        v-for="todo in todoASC"
        :key="todo.id"
        :class="['card p-0 mb-4 shadow-sm', { done: todo.done }]"
      >
        <div
          class="card-body d-flex align-items-center justify-content-evenly gap-2"
        >
          <input class="form-check-input" type="checkbox" v-model="todo.done" />
          <span>{{ todo.category }} Task</span>

          <span>
            <input
              class="form-control border-0 bg-white"
              type="text"
              v-model="todo.content"
            />
          </span>
          <span>
            <button
              class="btn btn-danger p-2 rounded-lg"
              @click="removeTodo(todo)"
            >
              Delete
            </button>
          </span>
        </div>
      </div>
    </section>
  </main>
</template>


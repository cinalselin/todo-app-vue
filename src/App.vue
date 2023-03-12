<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="name here"
                          v-model="name"/>
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create a todo</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
            type="text"
            placeholder="z.B erstelle ein Video"
            v-model="inputContent">
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
                type="radio"
                name="category"
                value="business"
                v-model="inputCategory">
            <span class="bubble business"></span>
            <div>Business</div>
          </label> <label>
          <input
              type="radio"
              name="category"
              id="category1"
              value="personal"
              v-model="inputCategory">
          <span class="bubble personal"></span>
          <div>Personal</div>
        </label>
        </div>
        <input type="submit" value="Add todo">
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <!--   In diesem Fall wird die Klasse todo-item immer angewendet, während die Klasse done hinzugefügt wird, wenn todo.done wahr ist.    -->
        <div v-for="todo in todosAsc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script setup>
// composition api
import {ref, onMounted, computed, watch} from "vue";

const todos = ref([]);
const name = ref('');

const inputContent = ref('');
const inputCategory = ref(null); // null default

// Sortiert die neuen TODOS. neueste zuletzt
const todosAsc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt;
}));

// =========== FUNCTION TODOs ===========
const addTodo = () => {
  // trim entfernt die leerzeichen
  if (inputContent.value.trim() === "" || inputCategory.value === null) {
    return;
  }
  // pushen in das array
  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime()
  });
  inputContent.value = "";
};
// ============ FUNKTION REMOVE TODOS
const removeTodo = todo => {
  const index = todos.value.indexOf(todo);
  /*Wenn die indexOf Methode das Element nicht im Array findet, gibt sie -1 zurück. Deshalb wird geprüft, ob der zurückgegebene Index nicht -1 ist, um sicherzustellen, dass das Element im Array gefunden wurde, bevor es entfernt wird. Wenn das Element nicht im Array gefunden wird, soll auch nichts entfernt werden.*/
  if (index !== -1) {
    todos.value.splice(index, 1);
  }
};
// ============= erklärung todos watch ===========
/*Die watch-Funktion überwacht die Variable todos auf Änderungen. Wenn sich der Wert von todos ändert, wird eine Funktion aufgerufen, die den neuen Wert von todos als Parameter erhält. In diesem Fall wird die Funktion genutzt, um die geänderten Todos in den lokalen Speicher des Browsers zu speichern, damit sie auch nach einem Neuladen der Seite erhalten bleiben. Das {deep: true}-Objekt ist eine Option, die dafür sorgt, dass auch tiefer verschachtelte Objekte innerhalb von todos beobachtet werden.*/
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal));
}, {deep: true});

watch(name, (newVal) => {
  localStorage.setItem('name', newVal); // der name wird beobachtet und geändert
});

/* ====================== ERKLÄRUNG onMounted===================

1. localStorage ist eine API, die es Webanwendungen ermöglicht, Daten im Browser des Benutzers zu speichern und abzurufen.
2. getItem('name') ist eine Funktion von localStorage, die den Wert eines Schlüssels 'name' aus dem Browser-Speicher abruft.
3. || '' ist eine logische Abkürzung in JavaScript, die bedeutet, dass wenn der Wert von localStorage.getItem('name') falsy ist (z.B. null, undefined oder false), dann wird anstelle des falsy-Werts ein leerer String ('') verwendet.*/

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem("todos")) || []
  ;
  // JSON.parse() ist eine JavaScript-Methode, die eine Zeichenfolge, die im JSON-Format vorliegt, in ein JavaScript-Objekt umwandelt.
});

/*
* In diesem Fall wird bei Aufruf der Funktion zunächst versucht, den gespeicherten Namen aus dem Browser-Speicher abzurufen. Dazu wird die Funktion localStorage.getItem('name') verwendet. Wenn ein solcher Name gefunden wird, wird dieser Name in die Variable name geschrieben, indem der Wert von name.value auf den gefundenen Namen gesetzt wird. Wenn kein Name gefunden wird, wird anstelle des Falsy-Werts (z.B. null, undefined oder false) ein leerer String verwendet.

Anschließend wird versucht, die gespeicherten To-Do-Listen aus dem Browser-Speicher abzurufen. Wenn gespeicherte To-Do-Listen gefunden werden, werden sie in die Variable todos geschrieben, indem der Wert von todos.value auf die geparste JSON-Darstellung der gespeicherten To-Do-Listen gesetzt wird. Wenn keine gespeicherten To-Do-Listen gefunden werden, wird anstelle des Falsy-Werts ein leeres Array verwendet.*/
</script>


<style scoped>

</style>

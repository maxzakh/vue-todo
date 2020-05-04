<template>
  <section class="container">
    <header>
      <h1 class="title">todos</h1>
      <input v-model="currentTodo" @keydown.enter="addTodo()" placeholder="What needs to be done?" />
    </header>
    <section>
      <ul class="todos">
        <li
          v-for="todo in todos"
          :key="todo.id"
          :class="{ todo: true, editing: editedTodo === todo, completed: todo.completed}"
          @dblclick="editTodo(todo)"
        >
          <div class="view">
            <input class="check" type="checkbox" v-model="todo.completed" />
            <div class="title">{{ todo.label }}</div>
            <button @click="removeTodo(todo)">&times;</button>
          </div>
          <input
            class="edit"
            v-model="todo.label"
            v-todo-html-focus="todo == editedTodo"
            @blur="cancelEdit(todo)"
            @keyup.esc="cancelEdit(todo)"
            @keyup.enter="doneEdit(todo)"
          />
        </li>
      </ul>
    </section>
    <footer>
      text
    </footer>
  </section>
</template>

<script>
////@ts-check
import Vue from "vue";

export default Vue.extend({
  data() {
    return {
      todos: [
        { id: "k9rt00sj", label: "apple", completed: false },
        { id: "k9rt01rf", label: "sauce", completed: false },
        { id: "k9rt02r0", label: "bacon", completed: false }
      ],
      currentTodo: "",
      editedTodo: null,
      originalEditedTodoValue: ""
    };
  },
  methods: {
    addTodo() {
      this.todos.push({
        id: Date.now().toString(36),
        label: this.currentTodo,
        completed: false
      });
      this.currentTodo = "";
    },
    removeTodo(todo) {
      var index = this.todos.indexOf(todo);
      this.todos.splice(index, 1);
    },
    editTodo(todo) {
      console.log("editTodo");
      // todo.completed = !todo.completed;
      this.editedTodo = todo;
      this.originalEditedTodoValue = todo.label;
    },
    doneEdit(todo) {
      console.log("doneEdit");
      this.editedTodo = null;
      if (!todo.label) {
        this.removeTodo(todo);
      }
    },
    cancelEdit(todo) {
      console.log("cancelEdit");
      if (!this.editedTodo) {
        return;
      }
      this.editedTodo = null;
      todo.label = this.originalEditedTodoValue;
    }
  },
  // a custom directive to wait for the DOM to be updated
  // before focusing on the input field.
  // http://vuejs.org/guide/custom-directive.html
  directives: {
    "todo-html-focus": function(el, binding) {
      if (binding.value) {
        el.focus();
      }
    }
  }
});
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 0;
  // min-height: 100vh;
  display: flex;
  justify-content: center;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

.container {
  display: flex;
  flex-flow: column;
  min-width: 70vw;
  margin-top: 80px;
  background-color: lightblue;
  // border: 1px solid red;
  box-shadow: 
    0 2px 4px 0 rgba(0, 0, 0, 0.2),
    0 25px 50px 0 rgba(0, 0, 0, 0.1);
  
  input {
    padding: 0.4em;
  }

  header {
    h1 {
      position: absolute;
      top: 0;
      width: 70vw;
      font-size: 2em;
      text-align: center;
      text-transform: uppercase;
    }

    input {
      width: 100%;
      font-size: 1.2em;
      border: none;
    }
  }

  .todos {
    padding: 0;
    background-color: #f7f7f7;

    li {
      list-style-type: none;

      .view {
        display: flex;
        // background-color: red;
        padding: 0.4em;
        align-items: center;

        .check {
          width: 16px;
          margin: 0;
        }

        .title {
          flex-grow: 1;
          margin-left: 0.4em;
        }

        button {
          border-radius: 50%;
        }
      }

      .edit {
        display: none;
        width: calc(100% - 16px - 0.4em);
        margin-left: calc(16px + 0.4em);
        font-size: 1em;
      }
    }

    li.editing .view {
      display: none;
    }

    li.editing .edit {
      display: block;
    }

    li.completed .title {
      text-decoration: line-through;
    }
  }
}
</style>
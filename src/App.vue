<template>
  <div class="container">
    <input v-model="currentTodo" @keydown.enter="addTodo()" placeholder="Add a todo" />
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
  </div>
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
  min-height: 100vh;
  display: flex;
  justify-content: center;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

.container {
  display: flex;
  flex-flow: column;
  margin-top: 5em;
  min-width: 70vw;
  // background-color: lightblue;

  input {
    padding: 0.4em;
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
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
      <div class="items-left">{{completed}} items left</div>
      <div class="static">
        <div class="items-all current-filter">All</div>
        <div class="items-active">Active</div>
        <div class="items-completed">Completed</div>
      </div>
      <div class="items-clear-completed" v-if="completed !== todos.length" @click="removeCompleted">Clear completed</div>
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
    removeCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
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
  computed: {
    completed() {
      let remaining = 0;
      this.todos.forEach(todo => {
        if (!todo.completed) {
          remaining++;
        }
      });
      return remaining;
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
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  // min-height: 100vh;
  display: flex;
  justify-content: center;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

$spacer: 0.4em;

.container {
  display: flex;
  flex-flow: column;
  min-width: 70vw;
  margin-top: 80px;
  // border: 1px solid red;
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 25px 50px 0 rgba(0, 0, 0, 0.1);

  input {
    padding: 0.6em;
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
      font-size: 1.3em;
      border: none;
    }
  }

  .todos {
    padding: 0;
    background-color: #fafafa;

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
          outline: none;
          border: 1px solid rgb(167, 167, 167);
          border-radius: 50%;
          padding: 1px 6px;
          color: rgb(122, 122, 122);
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

  footer {
    position: relative;
    display: flex;
    align-items: center;
    padding: 0 $spacer;
    height: 3em;
    background-color: #fafafa;
    color: rgb(90, 90, 90);
    // font-size: .9em;

    // & > * {
    //   border: 1px solid red;
    // }

    > :first-child {
      flex-basis: 6em;
    }

    .static {
      flex: 1 1;

      display: flex;
      justify-content: center;

      div {
        padding: 0.1em $spacer;
        cursor: pointer;
      }

      .current-filter {
        border: 1px dashed rgb(109, 109, 109);
        border-radius: 3px;
      }
    }

    .items-clear-completed {
      cursor: pointer;
    }
  }

  footer::before {
    content: "";
    position: absolute;
    height: 100px;
    left: 0;
    right: 0;
    z-index: -1;
    // top: 0;
    bottom: 0;
    box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2), 0 8px 0 -3px #f6f6f6,
      0 9px 1px -3px rgba(0, 0, 0, 0.2), 0 16px 0 -6px #f6f6f6,
      0 17px 2px -6px rgba(0, 0, 0, 0.2);
  }
}
</style>
<template>
  <div class="todo-app">
    <div class="todo-app__actions">
      <div class="filters">
        <button
          :class="{ active: filter === 'all' }"
          @click="changeFilter('all')"
        >
          모든 항목 ({{ todos.length }})
        </button>
        <button
          :class="{ active: filter === 'completed' }"
          @click="changeFilter('completed')"
        >
          완료된 항목 ({{ completedCount }})
        </button>
      </div>

      <div class="actions">
        <input v-model="allDone" type="checkbox" />
        <button @click="clearCompleted">완료된 항목 삭제</button>
      </div>
    </div>

    <hr />

    <div class="todo-app__list">
      <todo-item
        v-for="todo in filteredTodos"
        :key="todo.id"
        :todo="todo"
        @update-todo="updateTodo"
        @delete-todo="deleteTodo"
      />
    </div>

    <hr />

    <todo-creater class="todo-app__creator" @create-todo="createTodo" />
  </div>
</template>
<script>
import low from "lowdb";
import LocalStorage from "lowdb/adapters/LocalStorage";
import cryptoRandomString from "crypto-random-string";
import _find from "lodash/find";
import _findIndex from "lodash/findIndex";
import _assign from "lodash/assign";
import _cloneDeep from "lodash/cloneDeep";
import _forEachRight from "lodash/forEachRight";

import TodoCreater from "./TodoCreater";
import TodoItem from "./TodoItem";

export default {
  name: "TodoApp",
  components: {
    TodoCreater,
    TodoItem
  },
  data() {
    return {
      db: null,
      todos: [],
      filter: "all"
    };
  },
  computed: {
    filteredTodos() {
      switch (this.filter) {
        case "all":
        default:
          return this.todos;
        case "active":
          return this.todos.filter(todo => !todo.done);
        case "completed":
          return this.todos.filter(todo => todo.done);
      }
    },
    activeCount() {
      return this.todos.filter(todo => !todo.done).length;
    },
    completedCount() {
      return this.todos.length - this.activeCount;
    },
    allDone: {
      get() {
        const length = this.todos.length;
        return length === this.completedCount && length > 0;
      },
      set(checked) {
        this.completeAll(checked);
      }
    }
  },
  created() {
    this.initDB();
  },
  methods: {
    initDB() {
      const adapter = new LocalStorage("todo-app"); //DB
      this.db = low(adapter);

      const hasTodos = this.db.has("todos").value();

      if (hasTodos) {
        this.todos = _cloneDeep(this.db.getState().todos);
      } else {
        this.db
          .defaults({
            todos: []
          })
          .write();
      }
    },
    createTodo(title) {
      const newTodo = {
        id: cryptoRandomString({ length: 10 }),
        title,
        createdAt: new Date(),
        updatedAt: new Date(),
        done: false
      };

      try {
        this.db
          .get("todos")
          .push(newTodo)
          .write();
      } catch (error) {
        console.error(error);
        return;
      }

      this.todos.push(newTodo);
    },
    updateTodo(todo, value) {
      try {
        this.db
          .get("todos")
          .find({ id: todo.id })
          .assign(value)
          .write();
      } catch (error) {
        console.error(error);
        return;
      }

      const foundTodo = _find(this.todos, { id: todo.id });
      _assign(foundTodo, value);
    },
    deleteTodo(todo) {
      try {
        this.db
          .get("todos")
          .remove({ id: todo.id })
          .write();
      } catch (error) {
        console.log(error);
        return;
      }

      const foundIndex = _findIndex(this.todos, { id: todo.id });
      this.$delete(this.todos, foundIndex);
    },
    completeAll(checked) {
      const newTodos = this.db
        .get("todos")
        .forEach(todo => {
          todo.done = checked;
        })
        .write();
      this.todos = _cloneDeep(newTodos);
    },
    clearCompleted() {
      _forEachRight(this.todos, todo => {
        if (todo.done) {
          this.deleteTodo(todo);
        }
      });
    },
    changeFilter(filter) {
      this.filter = filter;
    }
  }
};
</script>

<style lang="scss">
button.active {
  font-weight: 900;
}
</style>

<template>
  <body>
    <h1 class="heading">TODO APP <i class="fab fa-vuejs fa-xs icon"></i></h1>
    <div class="inputDiv">
      <input
        class="input"
        type="text"
        v-model="todo"
        placeholder="Enter your todo here"
      />
    </div>
    <div class="inputDiv">
      <input
        class="input"
        type="text"
        v-model="todoDesc"
        placeholder="Enter your description here"
      />
    </div>
    <div class="inputPriority">
      <input
        class="input"
        type="text"
        @keypress="validateNumber"
        v-model="priority"
        placeholder="Enter your priority here"
      />
    </div>
    <div class="inputDiv">
      <input
        class="input"
        type="text"
        v-model="todoCat"
        placeholder="Enter your category here"
      />
    </div>
    <div class="filter-box">
      <select class="form-control" v-model="selectedCategory">
        <option value="" selected enabled>All</option>
        <option
          v-for="(cat, index) in dropDown"
          :key="index"
          style="background-color: white; color: black; font-weight: bold"
        >
          {{ cat }}
        </option>
      </select>
    </div>
    <!-- <div class="dropdown">
            <button class="dropbtn">Category</button>
              <div class="dropdown-content" v-for="(todo,index) in todos" :key="index">
                <button class="filterButton" @click="filter(index)">{{todo.cat}}</button>
              </div>
          </div> -->
    <!-- <div id="output-box"></div> -->
    <h4 v-if="isError">Text field required.</h4>
    <h4 v-if="isErrorNum">Please enter number only.</h4>
    <button class="addButton" @click="storeTodo">
      <i class="">Add</i>
    </button>
    <br />
    <div class="container">
      <div class="item" v-for="(todo, index) in computed_items" :key="index">
        <div
          class="itemInput"
          :class="{ strikeout: todo.isStrikedOff == true }"
        >
          {{ todo.seq }} -
          {{ todo.name }}
          <div class="desc" style="white-space: pre-wrap">
            {{ todo.desc }} - {{ todo.cat }}
          </div>
        </div>
        <button class="doneTodo" @click="doneTodo(index)">Done</button>
        <button class="removeTodo" @click="removeTodo(index)">Remove</button>
      </div>
    </div>
    <h1 class="sub-heading">
      REMOVED TODOS <i class="fas fa-trash-alt fa-xs icon"></i>
    </h1>
    <div class="sub-container">
      <div class="items">
        <div
          class="removedItem"
          v-for="(todo, index) in removedTodos"
          :key="index"
        >
          <div class="itemInput strikeout">
            {{ todo.seq }} -
            {{ todo.name }}
            <div class="desc" style="white-space: pre-line">
              {{ todo.desc }} - {{ todo.cat }}
            </div>
          </div>
          <button class="retrieveTodo" @click="retrieveTodo(index)">
            Retrieve
          </button>
          <button class="deleteTodo" @click="deleteTodo(index)">Delete</button>
        </div>
      </div>
    </div>
    <div id="footer">
      <p><a href="https://github.com/soham-dixit/todo-app-vuejs-cli" target="_blank">Source code</a></p>
    </div>
  </body>
</template>
<script>
export default {
  name: "TodoApp",
  data() {
    return {
      todo: "",
      priority: "",
      todos: [],
      todoDesc: "",
      category: "",
      removedTodos: [],
      isError: false,
      isErrorNum: false,
      selectedCategory: "",
    };
  },

  computed: {
    computed_items: function () {
      if (this.selectCategory !== "") {
        let filtertype = this.todos.filter((a) => {
          if (a.cat === this.selectedCategory) {
            return a;
          }
        });
        return filtertype;
      } else {
        return this.todos;
      }
    },

    dropDown() {
      let arr = [];
      this.todos.forEach((e) => {
        arr.push(e.cat);
      });
      let unique = arr.filter((item, i, ar) => ar.indexOf(item) === i);
      return unique;
    },
  },
  methods: {
    validateNumber() {
      let keyCode = event.keyCode;
      if (keyCode < 48 || keyCode > 57) {
        event.preventDefault();
        this.isErrorNum = true;
      } else {
        this.isErrorNum = false;
      }
    },
    storeTodo() {
      if (
        this.todo != "" &&
        this.todoDesc != "" &&
        this.todoCat != "" &&
        this.priority != ""
      ) {
        this.todos.push({
          name: this.todo,
          desc: this.todoDesc,
          cat: this.todoCat,
          seq: Number(this.priority),
          isStrikedOff: false,
        });

        this.todos.sort((a, b) => {
          return a.seq - b.seq;
        });
        this.priority = "";
        this.todo = "";
        this.todoDesc = "";
        this.todoCat = "";
        this.isError = false;
      } else {
        this.isError = true;
      }
    },
    removeTodo(index) {
      this.removedTodos.push(...this.todos.splice(index, 1));
    },
    deleteTodo(index) {
      this.removedTodos.splice(index, 1);
    },
    doneTodo(index) {
      this.todos[index].isStrikedOff = true;
    },
    retrieveTodo(index) {
      this.todos.push(...this.removedTodos.splice(index, 1));
      this.todos.sort((a, b) => {
        return a.seq - b.seq;
      });
    },
  },

  mounted() {
    console.log("App Mounted!");
    if (localStorage.getItem("todos"))
      this.todos = JSON.parse(localStorage.getItem("todos"));
    if (localStorage.getItem("removedTodos"))
      this.removedTodos = JSON.parse(localStorage.getItem("removedTodos"));
  },

  watch: {
    todos: {
      handler() {
        console.log("Todos changed!");
        localStorage.setItem("todos", JSON.stringify(this.todos));
      },
      deep: true,
    },
    removedTodos: {
      handler() {
        console.log("Todos changed!");
        localStorage.setItem("removedTodos", JSON.stringify(this.removedTodos));
      },
      deep: true,
    },
  },
};
</script>
<style></style>

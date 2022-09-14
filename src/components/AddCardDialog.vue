<template>
  <div class="modal" :class="{ 'position-unset': !ifDialog }">
    <div :class="{ 'modal-bg': ifDialog }"></div>
    <div class="todo-wrapper">
      <form>
        <input
          type="text"
          class="input-todo"
          placeholder="Title todo"
          v-model="form.title"
        />
        <input
          type="text"
          class="input-todo"
          v-bind:class="{ active: new_todo }"
          placeholder="todo"
          v-model="new_todo"
          @keydown.enter="addItem"
        />
        <div
          class="btn btn-add"
          v-bind:class="{ active: new_todo }"
          @click="addItem"
        >
          +
        </div>
      </form>
      <div>
        <transition-group name="todo-item" tag="ul" class="todo-list">
          <li v-for="item in pending" v-bind:key="item.title">
            <input
              class="todo-checkbox"
              v-bind:id="'item_' + item.id"
              v-model="item.done"
              type="checkbox"
            />
            <label v-bind:for="'item_' + item.id"></label>

            <span class="todo-text">{{ item.title }}</span>
            <span class="delete" @click="deleteItem(item)"></span>
            <span class="edit" @click="editItem(item)"></span>
          </li>
        </transition-group>
        <transition-group name="todo-item" tag="ul" class="todo-list archived">
          <li v-for="item in completed" v-bind:key="item.title">
            <input
              class="todo-checkbox"
              v-bind:id="'item_' + item.id"
              v-model="item.done"
              type="checkbox"
            />
            <label v-bind:for="'item_' + item.id"></label>
            <span class="todo-text">{{ item.title }}</span>
            <span class="delete" @click="deleteItem(item)"></span>
            <span class="edit" @click="editItem(item)"></span>
          </li>
        </transition-group>
      </div>
      <div class="card-footer">
        <button v-if="id === undefined" class="btn-grey" @click="closeDialog">
          Close
        </button>
        <button v-else class="btn-grey" @click="resetDialog">Reset</button>
        <button v-if="id != undefined" class="btn-red" @click="deleteDialog">
          Delete
        </button>
        <button class="btn-green" @click="saveDialog">Save</button>
      </div>
    </div>
  </div>
  <ConfirmDialog
    v-model:show="dialog.show"
    :title="dialog.title"
    @change="closeConfirmDialog"
  />
</template>

<script>
import ConfirmDialog from "./ConfirmDialog.vue";
export default {
  props: {
    id: String,
    ifDialog: {
      type: Boolean,
      default: false,
    },
  },
  emits: ["close"],
  data: () => ({
    form: {
      id: null,
      title: "",
      list: [],
    },
    dialog: {
      show: false,
      title: "",
      event: "",
    },
    new_todo: "",
    showComplete: true,
  }),
  mounted() {
    if (this.id) {
      this.loadTodos();
    }
  },
  methods: {
    deleteDialog() {
      this.dialog.title = "Do you really want to delete?";
      this.dialog.event = "Delete";
      this.dialog.show = true;
    },
    resetDialog() {
      this.dialog.title = "Do you really want to undo your changes?";
      this.dialog.event = "Reset";
      this.dialog.show = true;
    },
    closeConfirmDialog(answer) {
      this.dialog.show = false;

      if (answer == "yes") {
        if (this.dialog.event == "Reset") {
          this.loadTodos();
        }
        if (this.dialog.event == "Delete") {
          this.deleteTodo();
          this.$router.push({ name: "home" });
        }
      }

      this.dialog.title = "";
      this.dialog.event = "";
    },

    saveDialog() {
      let isNew = this.id === undefined;
      if (isNew) {
        this.form.id = Date.now();
        this.addTodos(this.form);
        this.form.id = null;
        this.form.title = "";
        this.form.list = [];
        this.closeDialog();
      } else {
        this.setTodos(this.form);
      }
    },
    closeDialog() {
      this.$emit("close");
    },
    loadTodos() {
      let todoList = localStorage.getItem("todo_list");
      if (typeof todoList === "string") {
        todoList = JSON.parse(todoList);
      } else {
        todoList = [];
      }
      let item = todoList.find((item) => item.id == this.id);
      if (typeof item === "object") {
        this.form.id = item.id;
        this.form.title = item.title;
        this.form.list = item.list;
      }
    },
    deleteTodo() {
      let todoList = localStorage.getItem("todo_list");
      if (typeof todoList === "string") {
        todoList = JSON.parse(todoList);
      } else {
        todoList = [];
      }

      let index = todoList.indexOf(todoList.find((val) => val.id == this.id));
      if (index != -1) {
        todoList.splice(index, 1);
      }
      localStorage.setItem("todo_list", JSON.stringify(todoList));
    },
    setTodos(item) {
      let todoList = localStorage.getItem("todo_list");
      if (typeof todoList === "string") {
        todoList = JSON.parse(todoList);
      } else {
        todoList = [];
      }

      let index = todoList.indexOf(todoList.find((val) => val.id == item.id));

      if (index != -1) {
        todoList[index] = item;
      }
      localStorage.setItem("todo_list", JSON.stringify(todoList));
    },
    addTodos(item) {
      let todoList = localStorage.getItem("todo_list");
      if (typeof todoList === "string") {
        todoList = JSON.parse(todoList);
      } else {
        todoList = [];
      }
      todoList.push(item);
      localStorage.setItem("todo_list", JSON.stringify(todoList));
    },
    // add a new item
    addItem() {
      let title = this.new_todo;
      if (title) {
        this.form.list.push({ title, done: false });
      }
      this.new_todo = "";
    },
    deleteItem(item) {
      this.form.list.splice(this.form.list.indexOf(item), 1);
    },
  },
  computed: {
    pending: function () {
      return this.form.list.filter(function (item) {
        return !item.done;
      });
    },
    completed: function () {
      return this.form.list.filter(function (item) {
        return item.done;
      });
    },
  },
  components: { ConfirmDialog },
};
</script>

<style scoped></style>

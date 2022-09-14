<template>
  <div class="home--grid">
    <TodoCard v-for="item in todoList" :key="item.id" :item="item" />

    <AddCard @click="openDialog()" />
  </div>
  <AddCardDialog v-if="dialogAdd" @close="closeDialog" if-dialog />
</template>

<script>
import TodoCard from "@/components/TodoCard.vue";
import AddCard from "@/components/AddCard.vue";
import AddCardDialog from "@/components/AddCardDialog.vue";
export default {
  name: "HomeView",
  components: { TodoCard, AddCard, AddCardDialog },
  data: () => ({
    dialogAdd: false,
    todoList: [],
  }),
  methods: {
    closeDialog() {
      this.dialogAdd = false;
      this.getTodos();
    },
    openDialog() {
      this.dialogAdd = true;
    },
    getTodos() {
      let todoList = localStorage.getItem("todo_list");
      if (typeof todoList === "string") {
        this.todoList = JSON.parse(todoList);
      } else {
        this.todoList = [];
      }
    },
  },
  mounted() {
    this.getTodos();
  },
};
</script>

<style scoped></style>

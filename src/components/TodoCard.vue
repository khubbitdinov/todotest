<template>
  <div @click="open(item.id)" class="basic-card">
    <div class="card-content">
      <span class="card-title">{{ item.title }}</span>
    </div>
    <ul class="todo-list todo-list-clear">
      <li
        v-for="(list, index) in item.list.filter((element, index) => index < 5)"
        v-bind:key="list.title"
      >
        <input
          class="todo-checkbox"
          v-bind:id="'item_' + index"
          v-model="list.done"
          type="checkbox"
        />
        <label v-bind:for="'item_' + item.id"></label>
        <span class="todo-text">{{ list.title }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    item: Object,
  },
  methods: {
    open(id) {
      this.$router.push({ name: "edit", params: { id } });
    },
  },
};
</script>

<style scoped></style>

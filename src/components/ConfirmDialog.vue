<template>
  <div v-if="show" class="modal">
    <div class="modal-bg"></div>
    <div class="todo-wrapper">
      {{ title }}

      <div class="card-footer">
        <button class="btn" @click="onYes">Yes</button>
        <button class="btn" @click="onNo">No</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: {
      type: Boolean,
      defauld: false,
    },
    title: String,
  },
  emits: ["change"],
  methods: {
    onYes() {
      this.$emit("change", "yes");
    },
    onNo() {
      this.$emit("change", "no");
    },
  },
};
</script>

<style scoped></style>

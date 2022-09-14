<template>
  <AddCardDialog v-model:id="$route.params.id" />
</template>

<script>
import AddCardDialog from "@/components/AddCardDialog.vue";
export default {
  name: "HomeView",
  components: { AddCardDialog },
  data: () => ({
    dialogAdd: false,
  }),
  methods: {
    closeDialog() {
      this.dialogAdd = false;
    },
    openDialog() {
      this.dialogAdd = true;
    },
  },
};
</script>

<style scoped></style>

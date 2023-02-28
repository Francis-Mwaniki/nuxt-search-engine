<template>
  <div>
    <!-- Larger animate loader for mounting with v-if=mounting -->
    <div v-if="mounting" class="flex justify-center items-center fixed inset-0 mx-auto">
      <div class="animate-spin">
        <img src="@/assets/searchr.png" alt="Logo" class="object-cover h-32 w-32" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["mounting"],
  data() {
    return {};
  },
};
</script>

<style scoped></style>

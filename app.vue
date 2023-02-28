<template>
  <div class="bg-slate-800 min-h-screen">
    <NuxtPage />
  </div>
</template>
<script>
export default {
  data() {
    return {};
  },
  /* reload on page route */
  watch: {
    $route(to, from) {
      window.location.reload();
    },
  },
};
</script>

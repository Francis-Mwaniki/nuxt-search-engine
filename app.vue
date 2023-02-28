<template>
  <div class="bg-slate-800 min-h-screen">
    <NuxtPage />
    <!-- Footer francis_search copyright with footer tag -->
    <footer class="bg-slate-800 text-white text-center text-xs p-3 fixed bottom-0 w-full">
      <div class="container mx-auto">
        <p>
          &copy; 2023
          <a href="https://francismwaniki.tech" class="underline hover:text-gray-400"
            >Francis Search</a
          >. All rights reserved.
        </p>
      </div>
    </footer>
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
<style>
@import url("https://fonts.googleapis.com/css2?family=Darker+Grotesque:wght@500;700&display=swap");
* {
  font-family: "Darker Grotesque", sans-serif;
  font-size: large;
  font-weight: 700;
  transition: all 0.2s ease-in-out;
  scroll-behavior: smooth;
  overflow-y: visible;
}
</style>

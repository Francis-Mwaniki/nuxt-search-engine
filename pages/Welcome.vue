<template>
  <div>
    <!-- Welcome page tell everything about franc search and franc chat  card with success card-->
    <div class="flex flex-col items-center justify-center h-screen">
      <div
        class="bg-slate-900 shadow-md rounded px-8 pt-6 pb-8 mb-4 mx-auto flex flex-col my-2 md:max-w-6xl max-w-md"
      >
        <div class="mb-4">
          <!-- image profile -->
          <img
            src="https://www.francismwaniki.tech/_nuxt/francis.432abb23.jpeg"
            alt="Francis Mwaniki"
            class="rounded-full h-32 w-28 mx-auto"
          />
          <!-- Developer with name -->
          <h1 class="text-gray-300 text-2xl text-center">Francis Mwaniki</h1>
          <p class="text-gray-300 text-lg">
            Welcome to francis search, a search engine that is built with love and
            passion.
          </p>
          <!-- feel free to search chat with AI -->
          <p class="text-gray-300 text-lg">Feel free to search and chat with AI.</p>
          <!-- home nuxt-link or chat -->
          <div class="flex justify-center items-center mx-auto flex-row gap-x-8">
            <p class="text-gray-300 text-lg">
              <nuxt-link to="/chatbot" class="text-indigo-600 hover:underline"
                >Chat with AI</nuxt-link
              >
            </p>

            <!-- Home nuxtlink -->
            <p class="text-gray-300 text-lg">
              <nuxt-link to="/" class="text-indigo-600 hover:underline">Home</nuxt-link>
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {};
</script>

<style scoped></style>

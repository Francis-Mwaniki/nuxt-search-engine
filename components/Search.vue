<template>
  <div class="flex flex-col h-screen">
    <div class="flex-none bg-gray-800 py-4 px-6">
      <div class="text-gray-300 text-lg font-semibold">My Search Engine</div>
    </div>
    <div class="flex-1 bg-gray-200 flex md:flex-row flex-col">
      <div class="flex-none bg-gray-200 w-64 py-4 px-6 border-r-2 border-indigo-600">
        <input
          type="text"
          v-model="query"
          placeholder="Search for movies, books, or products..."
          class="block w-full rounded-md bg-white border-gray-300 shadow-sm py-2 px-3 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 justify-self-stretch"
        />
        <button
          class="mt-3 w-full inline-flex items-center justify-center border border-transparent rounded-md shadow-sm py-2 px-4 bg-indigo-600 text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          @click="search"
        >
          Search
        </button>
      </div>
      <div class="flex-1 p-6">
        <div v-if="isLoading" class="text-gray-700">Loading...</div>
        <div v-else-if="error" class="text-red-500">{{ error }}</div>
        <div v-else>
          <div v-if="results.length === 0" class="text-gray-700">No results found.</div>
          <div v-else>
            <div v-for="result in results" :key="result.id" class="mb-4">
              <h3 class="text-lg font-semibold">{{ result.title }}</h3>
              <p class="text-gray-700">{{ result.description }}</p>
              <a :href="result.link" class="text-indigo-600 hover:underline">{{
                result.link
              }}</a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: "",
      results: [
        {
          id: 1,
          title: "The Lord of the Rings",
          description: "A trilogy of epic fantasy adventure films.",
          link: "https://www.imdb.com/title/tt0903624/",
        },
        {
          id: 2,
          title: "The Hobbit",
          description: "A fantasy adventure film.",
          link: "https://www.imdb.com/title/tt0903624/",
        },
        {
          id: 3,
          title: "The Silmarillion",
          description: "A collection of tales and legends.",
          link: "https://www.imdb.com/title/tt0903624/",
        },
      ],
      isLoading: false,
      error: null,
    };
  },
  methods: {
    async search() {
      this.isLoading = true;
      this.error = null;
      try {
        const response = await fetch("http://localhost:3000/search", {
          params: {
            q: this.query,
          },
        });
        this.results = response.data;
      } catch (error) {
        this.error = error.message;
      }
      this.isLoading = false;
    },
  },
};
</script>

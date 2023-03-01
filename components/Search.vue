<template>
  <div v-show="results">
    <a
      :href="result.link"
      v-for="result in results.results"
      :key="result"
      class="flex flex-row gap-x-4 mx-auto justify-center items-center hover:bg-slate-700 py-1 rounded px-2 cursor-pointer"
    >
      <!-- div img for og_image -->
      <div class="flex-none">
        <!-- on hover show visit with enclosed borders with a redirect icon -->
        <a :href="result.link">
          <img
            :src="result.og_image"
            class="object-cover rounded-full md:h-16 md:w-16 h-10 w-10 text-white"
          />
        </a>
      </div>
      <div class="mb-4 flex justify-start items-start mx-auto flex-col lg:max-w-2xl">
        <h3 class="md:text-lg text-sm font-semibold text-gray-300 md:flex-none hidden">
          {{ result.title }}
        </h3>
        <h3 class="md:text-lg text-sm font-semibold text-gray-300 md:hidden flex-none">
          {{ result.title.length ? result.title.substring(0, 30) : "" }}
        </h3>
        <p class="text-gray-400 md:text-base text-sm md:block hidden">
          {{ result.snippet }}
        </p>
        <!-- splice of snippets on sm screens -->
        <p class="text-gray-300 md:text-base text-xs md:hidden block">
          {{ result.snippet.length ? result.snippet.substring(0, 30) : "" }}...
        </p>
        <a
          :href="result.link"
          class="text-indigo-600 hover:underline md:text-base text-xs flex-wrap"
          >{{ result.link }}</a
        >
      </div>
    </a>
    <!-- test results -->
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

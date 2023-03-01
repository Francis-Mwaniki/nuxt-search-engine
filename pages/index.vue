<template>
  <div>
    <MountLoader :mounting="mounting" />
    <div class="flex flex-col items-center justify-center">
      <div class="mb-6 flex flex-row justify-center items-center">
        <!-- francis search engine with h2 tag -->
        <h2 class="text-3xl font-bold text-gray-300 nav-font">franc_Search</h2>
        <img src="@/assets/searchr.png" alt="Logo" class="object-cover h-20 w-20" />
      </div>
      <!--   <div class="flex-none bg-gray-800 py-4 px-6 w-full mb-3">
        <div class="text-gray-300 text-lg font-semibold">My Search Engine</div>
      </div> -->
      <div
        class="flex items-center w-full mx-auto mb-4 border rounded-full lg:max-w-2xl hover:shadow-md"
      >
        <div class="pl-5">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="w-5 h-5 text-gray-400"
            @click="search"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            stroke-width="2"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
            />
          </svg>
        </div>
        <input
          type="text"
          v-model="query"
          placeholder="Search for movies, books, or anything..."
          @keydown.enter="search"
          class="w-full bg-transparent rounded-full py-[14px] pl-4 outline-none text-gray-300"
        />
        <div class="pr-5">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="w-5 h-5 text-gray-400 text-blue-600 cursor-pointer"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            stroke-width="2"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z"
            />
          </svg>
        </div>
      </div>
      <div class="flex gap-x-6">
        <button
          class="px-3 py-2 md:text-base text-sm font-light cursor-pointer hover:shadow bg-gray-50 rounded"
        >
          Perfomant Search
        </button>
        <NuxtLink
          to="/chatbot"
          class="px-3 py-2 md:text-base text-sm font-light cursor-pointer hover:shadow bg-gray-50 rounded"
        >
          Chat with AI
        </NuxtLink>
      </div>
      <div class="mt-6">
        <div class="text-sm text-gray-300">
          Searches offered in:
          <span class="ml-2 text-blue-700">
            <a href="https://www.google.com" target="_blank">Google</a></span
          >
          <!-- dynamic a href -blank this.query -->
        </div>
      </div>
      <!-- if results length===0 -->
      <div v-if="results.length === 0" class="text-gray-300">Nothing to Show!.</div>
      <div class="flex-1 p-6">
        <!-- v-if loading skeleton loader  -->
        <div v-if="isLoading" class="text-gray-700">
          <!-- skeleton cards loaders with animate pulse -->
          <div
            class="animate-pulse flex flex-row gap-x-4 mx-auto justify-center items-center"
          >
            <!-- div img for og_image -->
            <div class="flex-none">
              <div class="rounded-full bg-none">
                <img
                  src="@/assets/loading-removebg-preview.png"
                  alt=""
                  class="rounded-full h-20 w-28 bg-none"
                />
              </div>
            </div>

            <!-- loading image -->
            <div class="rounded-full bg-none">
              <img
                src="@/assets/loading-removebg-preview.png"
                alt=""
                class="rounded-full h-20 w-28 bg-none"
              />
            </div>
          </div>
        </div>

        <!--  <div v-if="results.length ? results.length === 0 : ''" class="text-gray-300">
            No results found.
          </div> -->
        <div class="m-4 md:m-0 max-w-xs md:max-w-6xl">
          <div class="flex flex-col">
            <a
              :href="result.link"
              v-for="result in results.results"
              :key="result.id"
              class="flex flex-col gap-2 p-4 hover:bg-slate-700 rounded-md"
            >
              <div class="flex-none">
                <a :href="result.link">
                  <img
                    :src="result.og_image"
                    class="object-cover h-24 w-full md:h-32 md:w-32 rounded-lg text-white border border-gray-500 hover:border-indigo-600"
                  />
                </a>
              </div>
              <div class="flex flex-col">
                <h3 class="text-lg font-semibold text-gray-300">{{ result.title }}</h3>
                <p class="text-gray-400 text-base">{{ result.snippet }}</p>
              </div>
              <div class="flex-none mt-2">
                <a
                  :href="result.link"
                  class="text-indigo-600 hover:underline text-base flex flex-wrap"
                  >{{ result.link.substring(0, 50) }}</a
                >
              </div>
            </a>
          </div>
        </div>
        <!-- Error alert card -->
        <div v-if="error" class="mx-4 my-2">
          <div
            class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative"
            role="alert"
          >
            <strong class="font-bold"> <Icon name="ic:outline-warning" /> Error!</strong>
            <span class="block sm:inline mx-4"> {{ error }}</span>

            <span class="absolute top-0 bottom-0 right-0 px-4 py-3">
              <svg
                class="fill-current h-6 w-6 text-red-500 absolute top-0 right-0"
                role="button"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                @click="clear"
              >
                <title>Close</title>
                <path
                  d="M14.348 14.849a1.2 1.2 0 0 1-1.697 0L10 11.819l-2.651 3.029a1.2 1.2 0 1 1-1.697-1.697l2.758-3.15-2.759-3.152a1.2 1.2 0 1 1 1.697-1.697L10 8.183l2.651-3.031a1.2 1.2 0 1 1 1.697 1.697l-2.758 3.152 2.758 3.15a1.2 1.2 0 0 1 0 1.698z"
                />
              </svg>
            </span>
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
      results: [],
      isLoading: false,
      responses: [],
      mounting: true,
      error: null,
      active: true,
    };
  },
  methods: {
    async search() {
      this.isLoading = true;
      this.error = null;
      try {
        /* check if query is empty */
        if (!this.query) {
          this.error = "Please enter a search query";
          this.isLoading = false;
          return;
        }
        const response = await fetch(
          `https://django-search-engine.vercel.app/api/search/?query=${this.query}/`,
          {
            method: "GET",
            headers: {
              "Content-Type": "application/json",
            },
          }
        );
        if (response.ok) {
          const data = await response.json();
          this.results = data;
          this.responses = data;
          console.log(this.responses);
        } else {
          const data = await response.json();
          console.log(data);
          this.error = data.details;
        }
      } catch (error) {
        this.error = error;
        this.results = [];
        this.isLoading = false;
      }
      setTimeout(() => {
        this.isLoading = false;
      }, 2000);
    },

    clear() {
      this.error = null;
      this.query = "";
      this.results = [];
      this.isLoading = false;
    },
  },
  mounted() {
    setTimeout(() => {
      this.mounting = false;
    }, 3000);
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Pacifico&family=Rubik:wght@600&family=Sacramento&display=swap");
.nav-font {
  font-family: "Pacifico", cursive;
  font-family: "Rubik", sans-serif;
  font-family: "Sacramento", cursive;
  user-select: none;
  text-underline-offset: 0.2rem;
  text-decoration: underline;
}
</style>

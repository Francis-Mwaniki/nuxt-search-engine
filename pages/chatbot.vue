<template>
  <div class="bg-slate-900 min-h-screen">
    <div
      wire:isSaving
      v-show="isSaving"
      class="fixed top-0 left-0 right-0 bottom-0 w-full h-screen z-50 overflow-hidden bg-gray-900 opacity-75 flex flex-col items-center justify-center"
    >
      <h2 class="text-center dark:text-pink-300 text-indigo-100 text-xl font-semibold">
        <img
          src="../assets/loading-removebg-preview.png"
          class="bg-slate-900 w-20 h-20"
          alt=""
        />
      </h2>
      <p class="sm:w-1/3 w-2/3 text-center text-white text-2xl">Saving.....</p>
      <ClientOnly>
        <button
          class="btn-3 ring-1 ring-white hover:bg-slate-800 bg-gray-900 rounded-lg"
          @click="isSaving = false"
        >
          Cancel
          <Icon name="ic:outline-cancel" />
        </button>
      </ClientOnly>
    </div>
    <!-- loading -->
    <div
      wire:load
      v-show="load"
      class="fixed top-0 left-0 right-0 bottom-0 w-full h-screen z-50 overflow-hidden bg-gray-900 opacity-75 flex flex-col items-center justify-center"
    >
      <h2 class="text-center dark:text-pink-300 text-indigo-100 text-xl font-semibold">
        <img
          src="../assets/loading-removebg-preview.png"
          class="bg-slate-900 h-20 w-20 rounded-full"
          alt=""
        />
      </h2>
      <p class="sm:w-1/3 w-2/3 text-center text-white text-2xl">processing.....</p>
      <ClientOnly>
        <button
          class="btn-3 ring-1 ring-white hover:bg-slate-800 bg-gray-900 rounded-lg"
          @click="load = false"
        >
          Cancel
          <Icon name="ic:outline-cancel" />
        </button>
      </ClientOnly>
    </div>
    <!-- end of loading -->
    <div
      class="main bg-slate-900 flex justify-center items-center mx-auto my-3 space-y-4"
    >
      <div
        v-if="messages.length === 0"
        class="flex justify-center items-center gap-x-2 flex-col mt-12"
      >
        <div class="flex justify-center gap-x-2 flex-row">
          <button class="btn-2" @click="welcome">
            <Icon name="ic:outline-emoji-objects" class="h-6 w-6" />welcome
          </button>
          <button class="btn-2" @click="intro">
            <Icon name="ic:outline-emoji-objects" class="h-6 w-6" />intro
          </button>
        </div>
        <!-- welcome to Franc Chat with a smily icon about this chatbot using h2 tag-->
        <div
          class="text-center dark:text-pink-300 text-indigo-100 text-xl font-semibold flex justify-center items-center mx-auto"
        >
          <img
            src="../assets/searchr.png"
            class="bg-slate-900 h-20 w-20 rounded-full"
            alt=""
          />
          <h2 class="text-base text-white">Welcome Franc_Chat</h2>
        </div>
      </div>
      <div id="chat_container">
        <!-- v-if messages====0 welcome with a message or introductory message -->

        <div
          class="wrapper m-2"
          v-for="message in messages"
          :class="{ ai: message.isAi }"
        >
          <div class="chat bg-slate-800 p-2">
            <div class="profile">
              <img
                :src="message.isAi ? bot : userr"
                :alt="message.isAi ? 'bot' : 'userr'"
              />
            </div>
            <div class="message">
              <template v-if="message.isLoading">
                <span v-text="loadingIndicator"></span>
              </template>
              <template v-else>
                <div class="mx-auto">
                  <span>{{ message.text }}</span>
                </div>
              </template>
            </div>
          </div>
        </div>
      </div>
      <form
        @submit.prevent="handleSubmit"
        class="form-input flex justify-center items-center gap-x-2 flex-row bg-slate-900 ring-1 ring-white md:m-1 m-2 rounded sm:rounded-2xl mb-3"
        @keyup.enter="handleSubmit"
      >
        <textarea
          type="text"
          name="prompt"
          class="max-w-2xl"
          :class="load ? 'bg-slate-500 cursor-not-allowed' : 'bg-slate-900'"
          :disabled="load"
          :placeholder="load ? 'Please wait while it loads....' : 'Add your ingredients?'"
          id="prompt"
          v-model="prompt"
        ></textarea>
        <button
          type="submit"
          class="btn bg-slate-800 float-right bottom-2"
          :class="load ? ' cursor-not-allowed' : ''"
        >
          <Icon name="bi:send-fill" class="h-6 w-6" /> Send
        </button>
      </form>
      <ClientOnly>
        <div class="flex justify-center items-center gap-x-2 flex-row my-3">
          <button class="btn-2 bg-slate-800" @click="clear">
            <Icon name="ic:sharp-delete-forever" class="h-6 w-6" />clear
          </button>
          <button class="btn-2 bg-slate-800" @click="refresh">
            <Icon name="uil:refresh" class="h-6 w-6" />refresh
          </button>
        </div>
      </ClientOnly>
    </div>
  </div>
</template>
<script>
import { ref, computed, onMounted, watch } from "vue";
import { useRouter } from "vue-router";
import bott from "@/assets/img/bot.svg";
import user_bot from "@/assets/img/user.svg";

export default {
  setup() {
    const prompt = ref("");
    const messages = ref([]);
    const loadingIndicator = ref("");
    let index = ref(0);
    let isSaving = ref(false);
    let loadInterval = null;
    let all_instructions = ref([]);
    let newInstructions = ref("");
    let botsvg = bott;
    let filteredData = ref([]);
    let usersvg = user_bot;
    const bot = computed(() => {
      return botsvg;
    });
    const userr = computed(() => {
      return usersvg;
    });
    const load = ref(false);

    function handleSubmit() {
      messages.value.push({
        isAi: false,
        text: prompt.value,
        index: ++index.value,
      });
      messages.value.push({
        isAi: true,
        text: "",
        isLoading: true,
        index: ++index.value,
      });

      const messageDiv = messages.value[messages.value.length - 1];

      loadInterval = setInterval(() => {
        loadingIndicator.value += ".";
        if (loadingIndicator.value === "....") {
          loadingIndicator.value = "";
        }
      }, 300);
      load.value = true;
      fetch("https://gpt-chat-sb66.onrender.com/", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          prompt: prompt.value,
        }),
      })
        .then((response) => {
          load.value = false;
          if (!response.ok) {
            throw new Error(response.text());
          } else {
            clearInterval(loadInterval);
            return response.json();
          }
        })
        .then((data) => {
          clearInterval(loadInterval);
          messageDiv.text = data.bot.trim();
          messageDiv.isLoading = false;
        })
        .catch((err) => {
          clearInterval(loadInterval);
          messageDiv.text = "Something went wrong";
          messageDiv.isLoading = false;
          alert(err);
        });
      prompt.value = "";
    }

    onMounted(() => {});
    const clear = () => {
      messages.value = [];
    };
    const refresh = () => {
      window.location.reload();
    };

    return {
      prompt,
      filteredData,
      all_instructions,
      messages,
      refresh,
      isSaving,
      clear,
      loadingIndicator,
      bot,
      index,
      load,
      userr,
      handleSubmit,
    };
  },
};
</script>
<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Alegreya+Sans:wght@100;300;400;500;700;800;900&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Alegreya Sans", sans-serif;
}

body {
  overflow: auto;
}

#chat_container {
  flex: 1;
  width: 100%;
  margin-top: 90px;
  height: 100%;
  overflow-y: scroll;

  display: flex;
  flex-direction: column;
  gap: 10px;

  -ms-overflow-style: none;
  scrollbar-width: none;

  padding-bottom: 20px;
  scroll-behavior: smooth;
}
.btn {
  border-radius: 999px;
  padding: 10px 20px;
  color: white;
}
.btn-2 {
  border-radius: 10px;
  padding: 10px 30px;
  color: white;
}
.btn-3 {
  padding: 10px 30px;
  color: white;
}

/* hides scrollbar */
#chat_container::-webkit-scrollbar {
  display: none;
}

.wrapper {
  width: 100%;
  padding: 15px;
}

.ai {
}
.form-input {
  position: relative;
  bottom: 0px;
  max-width: 1280px;
  color: black;
  margin: 0 auto;
  padding: 10px;
  display: flex;
  flex-direction: row;
  gap: 10px;
}
.chat {
  width: 100%;
  max-width: 1280px;
  margin: 0 auto;

  display: flex;
  flex-direction: row;
  align-items: flex-start;
  gap: 10px;
}

.profile {
  width: 36px;
  height: 36px;
  border-radius: 5px;

  background: #5436da;

  display: flex;
  justify-content: center;
  align-items: center;
}

.ai .profile {
  background: #10a37f;
}

.profile img {
  width: 60%;
  height: 60%;
  object-fit: contain;
}
.main {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 5px;
  overflow: auto;
}
.message {
  flex: 1;

  color: #ffffff;
  font-size: 20px;

  max-width: 100%;
  overflow-x: scroll;

  /*
           * white space refers to any spaces, tabs, or newline characters that are used to format the CSS code
           * specifies how white space within an element should be handled. It is similar to the "pre" value, which tells the browser to treat all white space as significant and to preserve it exactly as it appears in the source code.
           * The pre-wrap value allows the browser to wrap long lines of text onto multiple lines if necessary.
           * The default value for the white-space property in CSS is "normal". This tells the browser to collapse multiple white space characters into a single space, and to wrap text onto multiple lines as needed to fit within its container.
          */
  white-space: pre-wrap;

  -ms-overflow-style: none;
  scrollbar-width: none;
}

/* hides scrollbar */
.message::-webkit-scrollbar {
  display: none;
}

textarea {
  width: 100%;

  color: rgb(255, 255, 255);
  font-size: 18px;

  padding: 10px 5px;
  background: transparent;
  border-radius: 5px;
  border: none;
  outline: none;
}

form img {
  width: 30px;
  height: 30px;
}
</style>

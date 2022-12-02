<script setup>
import { reactive, ref, watch } from 'vue'

function createDebounce(delayMs) {
  let timeout = null
  return function (fnc) {
    clearTimeout(timeout)
    timeout = setTimeout(() => {
      fnc()
    }, delayMs || 500)
  }
}

const searchTermDebounce = createDebounce(300)

const searchTerm = ref('')
const results = ref([])
const loading = ref(false)

const findProducts = async term => {
  if (!term) {
    results.value = []
  } else {
    try {
      loading.value = true
      const requestResults = await fetch('https://dummyjson.com/products/search?q=phone')
      const resultJson = await requestResults.json()
      console.log({ products: resultJson.products })
      results.value = resultJson.products
    } finally {
      loading.value = false
    }
  }
}

watch(searchTerm, newTerm => searchTermDebounce(() => findProducts(newTerm)))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />

    <div v-if="loading" class="loader ease-linear rounded-full border-4 border-t-4 h-12 w-12"></div>
    <ul v-else class="list-disc">
      <li v-if="!results.length">Display suggestions here</li>
      <li v-else v-for="result in results">{{ result.title }}</li>
    </ul>
  </div>
</template>
<style>
.loader {
  border-top-color: #3498db;
  -webkit-animation: spinner 1.5s linear infinite;
  animation: spinner 1.5s linear infinite;
}

@-webkit-keyframes spinner {
  0% {
    -webkit-transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
  }
}

@keyframes spinner {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>

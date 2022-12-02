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

const findProducts = async term => {
  if (!term) {
    results.value = []
  } else {
    const requestResults = await fetch('https://dummyjson.com/products/search?q=phone')
    const resultJson = await requestResults.json()
    console.log({ products: resultJson.products })
    results.value = resultJson.products
  }
}

watch(searchTerm, newTerm => searchTermDebounce(() => findProducts(newTerm)))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <div class="spinner-border animate-spin inline-block w-8 h-8 border-4 rounded-full" role="status">
      <span class="visually-hidden"></span>
    </div>
    <ul class="list-disc">
      <li v-if="!results.length">Display suggestions here</li>
      <li v-else v-for="result in results">{{ result.title }}</li>
    </ul>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

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
  results.value.push(term)
}

watch(searchTerm, newTerm => searchTermDebounce(() => findProducts(newTerm)))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <ul class="list-disc">
      <li v-if="!results.length">Display suggestions here</li>
      <li v-else v-for="result in results">{{ result }}</li>
    </ul>
  </div>
</template>

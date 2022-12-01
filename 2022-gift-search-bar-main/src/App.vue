<script setup>
import { ref, watch, computed } from 'vue'
import { useDebounceFn, useFetch } from '@vueuse/core'

const searchTerm = ref('')
const url = computed(() => `https://dummyjson.com/products/search?q=${searchTerm.value}&limit=5`)

const isLoading = ref(false)
const result = ref([])
const err = ref('')
// debounce 300ms

// fetch products from https://dummyjson.com/docs/products

// if search term is empty string then clear result

const findProducts = useDebounceFn(async term => {
  // console.log(term)
  try {
    if (term.length === 0) return (products.value = [])

    isLoading.value = true

    const { data } = await useFetch(url)
    result.value = data
  } catch (error) {
    err.value = error
  } finally {
    isLoading.value = false
  }
}, 300)

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model.trim="searchTerm" placeholder="Start typing..." />
    <!-- add error message -->
    <!-- <div v-if="err.length">Error: {{ err.message }}. Try again</div> -->

    <ul v-if="!isLoading && result.length > 0" class="list-disc">
      <li v-for="product in result.products">{{ product.title }}</li>
    </ul>
    <!-- add a loading spinner / message -->
    <div v-if="isLoading">No products found</div>
  </div>
</template>

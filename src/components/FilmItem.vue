<template>
  <div v-if="watched" class="flex space-x-2 mb-2 hover:bg-yellow-300 duration-300 px-1">
    <div class="h-22 w-15">
      <img v-if="getImdbPoster(title)" :src="getImdbPoster(title)" alt="" class="object-cover" />
      <div v-else class="size-full bg-stone-800" />
    </div>
    <div class="flex-1 flex justify-between">
      <div class="flex-wrap">{{ rank }}. {{ title }}</div>
      <div class="text-right">
        <div>{{ rating.toFixed(1) }}</div>
        <div>{{ stars }}</div>
      </div>
    </div>
  </div>
  <div v-else class="flex justify-between bg-red-950 text-white duration-300 px-1">
    <div>{{ rank }}. {{ title }}</div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const props = defineProps({
  rank: Number,
  title: String,
  rating: [String, Number], //it's a union type
  watched: Boolean,
})

const halfRating = props.rating / 2

const stars = computed(
  () =>
    '☆'.repeat(5 - halfRating) +
    '⯫'.repeat(halfRating - Math.floor(halfRating) > 0 ? 1 : 0) +
    '★'.repeat(halfRating),
)

const imdbResults = ref(null)

const getImdbPoster = (title) => {
  return imdbResults.value?.filter((item) => item.query === title)[0]?.titles[0]?.primaryImage.url
}

onMounted(async () => {
  try {
    const response = await fetch('/imdbapi.json')
    imdbResults.value = await response.json()
  } catch (error) {
    console.error('Failed to load imbdapi.json', error)
    return
  }
})
</script>

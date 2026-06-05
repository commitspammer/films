<template>
  <div v-if="watched" class="flex justify-between hover:bg-yellow-300 duration-300 px-1">
    <p>{{ rank }}. {{ title }}</p>
    <p>{{ rating }}★</p>
  </div>
  <div v-else class="flex justify-between bg-red-950 text-white duration-300 px-1">
    <p>{{ rank }}. {{ title }}</p>
  </div>
</template>

<script setup>
defineProps({
  rank: Number,
  title: String,
  rating: [String, Number],
  watched: Boolean,
})
</script>

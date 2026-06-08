<template>
  <main class="p-4">
    <h1>Test View</h1>
    <p>Loaded: ({{ counter }}/{{ total }})</p>
    <div @click="cancelState = 'cancelling'" class="border-2 w-fit px-2 select-none">
      {{
        cancelState === 'cancelling'
          ? 'Cancelling...'
          : cancelState === 'cancelled'
            ? 'Cancelled'
            : 'CANCEL'
      }}
    </div>
    <p>{{ output }}</p>
  </main>
</template>

<script setup>
import { ref, onMounted } from 'vue'
const output = ref('[]')
const counter = ref(0)
const total = ref(0)
const cancelState = ref('notCancelled')
onMounted(async () => {
  try {
    const response = await fetch('/films.json')
    let films = await response.json()
    films = films.slice(74)
    total.value = films.length
    for (const film of films) {
      if (cancelState.value === 'cancelling') {
        cancelState.value = 'cancelled'
        throw new Error('Cancelled')
      }
      await new Promise((resolve) => setTimeout(resolve, 10000))
      const response2 = await fetch(
        'https://api.imdbapi.dev/search/titles?query=' + film.name + '&limit=3',
      )
      const searchResults = await response2.json()
      if (!searchResults.titles) {
        film.posterUrl = ''
        output.value = JSON.stringify(films.filter((f) => f.posterUrl !== undefined))
        counter.value += 1
        console.log(film)
        continue
      }
      const titles = searchResults.titles
      for (const title of titles) {
        if (title.type === 'tvSeries') {
          continue
        }
        film.posterUrl = title.primaryImage.url
        output.value = JSON.stringify(films.filter((f) => f.posterUrl !== undefined))
        break
      }
      counter.value += 1
      console.log(film)
    }
    console.log(JSON.stringify(films))
    output.value = JSON.stringify(films)
  } catch (error) {
    console.error('FAILED', error)
  }
})
</script>

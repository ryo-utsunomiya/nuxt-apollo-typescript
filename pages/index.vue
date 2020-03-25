<template>
  <div :class="$style.container">
    <h1>Star wars films</h1>
    <ul>
      <li v-for="film in films" :key="film.episodeID">
        {{ film.title }}
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

interface Film {
  episodeID: number
  title: string
}
interface Edge {
  node: Film
}
interface Data {
  films: Film[]
}

export default Vue.extend({
  data(): Data {
    return {
      films: []
    }
  },
  async created() {
    const query = `
{
  allFilms(first: 3) {
    edges {
      node {
        episodeID
        title
      }
    }
  }
}
`
    const res = await fetch(
      'https://swapi-graphql.netlify.com/.netlify/functions/index',
      {
        method: 'POST',
        body: JSON.stringify({
          query
        }),
        headers: {
          'Content-Type': 'application/json'
        }
      }
    ).then((res) => res.json())

    this.films = res.data.allFilms.edges.map((e: Edge) => ({ ...e.node }))
  }
})
</script>

<style module>
.container {
  margin: 10px;
}
</style>

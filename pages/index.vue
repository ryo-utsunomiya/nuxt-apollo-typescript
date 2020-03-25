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

// GraphQLのレスポンスに型をつける
interface Film {
  episodeID: number
  title: string
}
interface Edge {
  node: Film
}
interface FilmConnection {
  edges: Edge[]
}
interface ResponseData {
  allFilms: FilmConnection
}
interface Response {
  data: ResponseData
}

// Vueのdataに型をつける
interface VueData {
  films: Film[]
}

export default Vue.extend({
  data(): VueData {
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
    ).then<Response>((res) => res.json())

    this.films = res.data.allFilms.edges.map((e) => ({ ...e.node }))
  }
})
</script>

<style module>
.container {
  margin: 10px;
}
</style>

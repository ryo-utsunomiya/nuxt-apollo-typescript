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
import gql from 'graphql-tag'

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

// Vueのdataに型をつける
interface Data {
  allFilms: FilmConnection
}

export default Vue.extend({
  data(): Data {
    return {
      allFilms: {
        edges: []
      }
    }
  },
  computed: {
    films(): Film[] {
      return this.allFilms.edges.map((e) => ({ ...e.node }))
    }
  },
  apollo: {
    allFilms: gql`
      query {
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
  }
})
</script>

<style module>
.container {
  margin: 10px;
}
</style>

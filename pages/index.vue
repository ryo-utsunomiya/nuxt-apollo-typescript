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
import { FilmsConnection, Film } from '~/lib/GraphQL/generated'

// Vueのdataに型をつける
interface Data {
  allFilms?: FilmsConnection
}

export default Vue.extend({
  data(): Data {
    return {
      allFilms: undefined
    }
  },
  computed: {
    films(): Film[] {
      if (this.allFilms == null || this.allFilms.edges == null) return []
      return this.allFilms.edges
        .map((e) => e?.node)
        .filter((f): f is Film => f != null)
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

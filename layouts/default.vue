<template>
  <v-app dark>
    <v-main class="bg">
      <!-- <v-container> -->
      <nuxt />
      <!-- </v-container> -->
    </v-main>
  </v-app>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator';
@Component
export default class Default extends Vue {
  title = 'gootalife-hacks';
}
</script>

<style scoped>
.v-application {
  background-color: black !important;
  background-attachment: fixed !important;
  margin: 0 !important;
  padding: 0 !important;
  height: 100vh;
}
</style>

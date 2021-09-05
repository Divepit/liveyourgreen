<template>
  <v-app>
    <v-app-bar app color="#FBC349" dark>
      <div class="d-flex align-center">
        <v-icon large class="mr-2">mdi-beehive-outline</v-icon>
        <span style="font-size: 1.7em" class="font-weight-light">Beecon</span>
      </div>
    </v-app-bar>
    <v-main>
      <router-view/>
    </v-main>
  </v-app>
</template>

<script>

export default {
  name: 'App',

  data: () => ({
    //
  }),
};
</script>

<style>
</style>

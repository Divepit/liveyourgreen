<template>
  <v-app>
    <v-app-bar app color="success" dark>
      <div class="d-flex align-center">
        <v-icon x-large class="mr-4">mdi-leaf</v-icon>
        <span style="font-size: 1.7em" class="font-weight-thin">Live your green</span>
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

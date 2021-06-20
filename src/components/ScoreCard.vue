<template>
  <v-card class="mx-auto" color="grey lighten-4" max-width="600">
    <v-card-title>
      <v-icon v-if="!checking" :color="checking ? 'gray' : 'success'" class="mr-12" size="64">
        mdi-leaf
      </v-icon>
      <v-progress-circular
          class="mr-12" size="64"
          v-else
          indeterminate
          color="success"
      ></v-progress-circular>

      <v-row align="start">
        <div class="text-caption grey--text text-uppercase mr-2">Living Score <br> {{Math.round(1000*marker.lat)/1000}},
          {{Math.round(100*marker.lng)/100}}
        </div>
        <div>
          <span class="text-h3 font-weight-black" v-text=" checking ? '' : avg"></span>
          <strong v-if="avg"></strong>
        </div>
      </v-row>
    </v-card-title>

    <v-sheet color="transparent">
      <v-sparkline v-if="!checking" :key="String(avg)" :smooth="16" :gradient="['#1feaea', '#ffd200','#f72047']" :line-width="1"
                   :value="subscores" auto-draw stroke-linecap="round"></v-sparkline>
    </v-sheet>
  </v-card>
</template>
<script>
export default {
  name: 'ScoreCard',
  props: {
    avg: {},
    checking: {},
    subscores: {},
    marker: {}
  }
}
</script>

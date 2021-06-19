<template>
  <div style="height: 100%; width: 100%">
    <div style="height: 100%; width: 100%;">
      <l-map
          v-if="showMap"
          :zoom="zoom"
          :center="center"
          :options="mapOptions"
          style="height: 100%; cursor: crosshair"
          @update:center="centerUpdate"
          @update:zoom="zoomUpdate"
          @click="addMarker"
      >
        <l-tile-layer
            :url="url"
            :attribution="attribution"
        />
        <l-marker :lat-lng="marker" @click="removeMarker()" :icon="icon"></l-marker>
        <l-control :position="'topright'" class="ma-4">
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
                <div class="text-caption grey--text text-uppercase mr-2">Living Score <br> {{Math.round(1000*marker.lat)/1000}}, {{Math.round(100*marker.lng)/100}}</div>
                <div>
                  <span class="text-h3 font-weight-black" v-text=" checking ? '' : avg"></span>
                  <strong v-if="avg"></strong>
                </div>
              </v-row>
            </v-card-title>

            <v-sheet color="transparent">
              <v-sparkline :key="String(avg)" :smooth="16" :gradient="['#f72047', '#ffd200', '#1feaea']" :line-width="3" :value="heartbeats" auto-draw stroke-linecap="round"></v-sparkline>
            </v-sheet>
          </v-card>
        </l-control>
      </l-map>
    </div>
  </div>
</template>

<script>
//sparkline
const exhale = ms =>
    new Promise(resolve => setTimeout(resolve, ms))
import { latLng, icon } from 'leaflet';
import {
  LMap, LTileLayer, LMarker, LControl
} from 'vue2-leaflet';
export default {
  name: 'Example',
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LControl,
  },
  data() {
    return {
      zoom: 13,
      center: latLng(47.37, 8.54),
      marker: latLng(47.37, 8.54),
      url: 'https://cartodb-basemaps-b.global.ssl.fastly.net/light_nolabels/{z}/{x}/{y}.png',
      urldark: 'https://cartodb-basemaps-{s}.global.ssl.fastly.net/dark_all/{z}/{x}/{y}.png',
      attribution:
          '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      currentZoom: 11.5,
      showParagraph: false,
      mapOptions: {
        zoomSnap: 0.5,
      },
      showMap: true,
      icon: icon({
        iconUrl: "https://image.flaticon.com/icons/png/512/684/684908.png",
        iconSize: [32, 32],
        iconAnchor: [16, 37]
      }),
      //sparkline
      checking: false,
      heartbeats: [],
      //image overlay
      bounds: [[8.258889841, 8.738077426], [47.235039561, 47.562982412]],
    };
  },
  //sparklines
  computed: {
    avg () {
      const sum = this.heartbeats.reduce((acc, cur) => acc + cur, 0)
      const length = this.heartbeats.length

      if (!sum && !length) return 0

      return Math.ceil(sum / length)
    },
  },
  created () {
    this.takePulse(false)
  },
  methods: {
    refillValues() {
      this.value.from({length: 40}, () => Math.floor(Math.random() * 40));
    },
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
    },
    // MARKER CLICK
    removeMarker() {
      this.marker = this.center;
    },
    addMarker(event) {
      this.marker = event.latlng;
      this.takePulse()
    },
    //sparklines
    heartbeat () {
      return Math.ceil(Math.random() * (120 - 80) + 80)
    },
    async takePulse (inhale = true) {
      this.checking = true

      inhale && await exhale(1000)

      this.heartbeats = Array.from({ length: 20 }, this.heartbeat)

      this.checking = false
    },
  },
};
</script>
<style>
.example-custom-control {
  background: #fff;
  padding: 0 0.5em;
  border: 1px solid #aaa;
  border-radius: 0.1em;
}
.custom-control-watermark {
  font-size: 200%;
  font-weight: bolder;
  color: lightgray;
  text-shadow: #555;
  background: white;
  padding: 15px;
  border-radius: 3px;
  -webkit-box-shadow: 3px 6px 13px -6px rgba(0,0,0,0.54);
  box-shadow: 3px 6px 13px -6px rgba(0,0,0,0.54);
}
</style>

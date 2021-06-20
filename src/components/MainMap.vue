<template>
  <div style="height: 100%; width: 100%">
    <link rel="stylesheet" href="https://unpkg.com/leaflet-geosearch@2.6.0/assets/css/leaflet.css">
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
        <l-marker
            :lat-lng="marker"
            @click="removeMarker()">
        </l-marker>
        <l-control
            :position="'topright'"
            class="ma-4"
        >
          <ScoreCard
              :avg="avg"
              :checking="checking"
              :subscores="subscores"
              :marker="marker"
          />
        </l-control>
        <l-control
            :position="'topright'"
            class="ma-4"
        >
          <v-geosearch :options="geosearchOptions" @click="this.marker = this.center"></v-geosearch>
        </l-control>
        <v-geosearch :options="geosearchOptions" ></v-geosearch>
      </l-map>
    </div>
  </div>
</template>

<script>
//sparkline
import ScoreCard from "@/components/ScoreCard";
import {icon, latLng} from 'leaflet';
import { Icon } from 'leaflet';
import {LControl, LMap, LMarker, LTileLayer} from 'vue2-leaflet';
import { OpenStreetMapProvider } from 'leaflet-geosearch';
import VGeosearch from 'vue2-leaflet-geosearch';
import axios from 'axios'

delete Icon.Default.prototype._getIconUrl;
Icon.Default.mergeOptions({
  iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
  iconUrl: require('leaflet/dist/images/marker-icon.png'),
  shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
});
const exhale = ms =>
    new Promise(resolve => setTimeout(resolve, ms))

export default {
  name: 'Example',
  components: {
    ScoreCard,
    LMap,
    LTileLayer,
    LMarker,
    LControl,
    VGeosearch
  },
  data() {
    return {
      geosearchOptions: { // Important part Here
        provider: new OpenStreetMapProvider(),
        //style: 'bar',
        autoClose: true,
        //keepResult: true
      },
      zoom: 13,
      center: latLng(47.37, 8.54),
      marker: latLng(47.37, 8.54),
      url: 'https://cartodb-basemaps-b.global.ssl.fastly.net/light_nolabels/{z}/{x}/{y}.png',
      attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
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
      subscores: [],
      //image overlay
      bounds: [[8.258889841, 8.738077426], [47.235039561, 47.562982412]],
    };
  },
  //sparklines
  computed: {
    avg () {
      const sum = this.subscores.reduce((acc, cur) => acc + cur, 0)
      const length = this.subscores.length

      if (!sum && !length) return 0

      return Math.ceil(sum / length)
    },
  },
  created () {
    this.takePulse(false)
    this.apiTest()
  },
  methods: {
    addMarker(event) {
      this.marker = event.latlng;
      this.takePulse()
    },
    centerUpdate(center) {
      this.currentCenter = center;
    },
    //sparklines
    subscore () {
      return Math.ceil(Math.random() * 100)
    },
    refillValues() {
      this.value.from({length: 40}, () => Math.floor(Math.random() * 40));
    },
    // MARKER CLICK
    removeMarker() {
      this.marker = this.center;
    },
    async takePulse (inhale = true) {
      this.checking = true

      inhale && await exhale(1000)

      this.subscores = Array.from({ length: 12 }, this.subscore)

      this.checking = false
    },
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    apiTest() {
      var config = {
        headers: {'Access-Control-Allow-Origin': '*'}
      };
      axios.get("https://127.0.0.1:5000/apitest/1/2", config).then((response) => {
        console.log(response.data)
      })
    }
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

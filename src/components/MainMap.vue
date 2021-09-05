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
            v-if="visible"
            :lat-lng="marker"
            @click="removeMarker()">
        </l-marker>
        <l-control
            :position="'topright'"
            class="ma-4"
        >
          <v-btn :color="selectorMode ? '#F8B51E' : ''" class="ma-0 py-6" @click="selectorMode = !selectorMode">
            <v-icon x-large>mdi-bee-flower</v-icon>
          </v-btn>
        </l-control>
        <v-geosearch :options="geosearchOptions" @click="visible = false"></v-geosearch>
      </l-map>
    </div>
  </div>
</template>

<script>
import {icon, latLng} from 'leaflet';
import { Icon } from 'leaflet';
import {LControl, LMap, LMarker, LTileLayer} from 'vue2-leaflet';
import { OpenStreetMapProvider, GeoSearchControl } from 'leaflet-geosearch';
import VGeosearch from 'vue2-leaflet-geosearch';

delete Icon.Default.prototype._getIconUrl;
Icon.Default.mergeOptions({
  iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
  iconUrl: require('leaflet/dist/images/marker-icon.png'),
  shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
});

const provider = new OpenStreetMapProvider()

export default {
  name: 'Example',
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LControl,
    VGeosearch
  },
  data() {
    return {
      geosearchOptions: { // Important part Here
        provider: provider,
        searchControl: new GeoSearchControl({
          provider: provider
        }),
        style: 'bar',
        autoClose: true,
        keepResult: true
      },
      zoom: 13,
      center: latLng(47.37, 8.54),
      marker: latLng(47.37, 8.54),
      url: 'https://cartodb-basemaps-b.global.ssl.fastly.net/light_nolabels/{z}/{x}/{y}.png',
      attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      currentZoom: 11.5,
      showParagraph: false,
      mapOptions: {
        zoomSnap: 3.7,
      },
      showMap: true,
      icon: icon({
        iconUrl: "https://image.flaticon.com/icons/png/512/684/684908.png",
        iconSize: [32, 32],
        iconAnchor: [16, 37]
      }),
      visible: true,
      selectorMode: false
      //image overlay
    };
  },
  created () {
  },
  methods: {
    addMarker(event) {
      if (this.selectorMode) {
        this.visible = true
        this.marker = event.latlng;
      }
      this.selectorMode = false
    },
    centerUpdate(center) {
      this.currentCenter = center;
    },
    // MARKER CLICK
    removeMarker() {
      this.visible = false;
    },
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
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

<template>
  <span>
    <v-map style="height: 100%; min-height: 100px; width: auto; padding: 200px;" :zoom="zoom" :center="center">
      <v-tilelayer :url="url" :attribution="attribution"></v-tilelayer>
      <v-geo-json :geojson="geojson" :options="options"></v-geo-json>
    </v-map>
  </span>
</template>

<script>
import Vue from 'vue';
import esri2geo from 'esri2geo';
import PopupContent from './_partials/PopupContent';

function onEachFeature(feature, layer) {
  const Content = Vue.extend(PopupContent);
  const popup = new Content({
    propsData: { feature },
  });
  layer.bindPopup(popup.$mount().$el);
}

export default {
  name: 'LeafletMap',
  props: {
    mapType: {
      type: String,
      required: true,
      default: 'pincode',
    },
    locality: {
      type: Object,
      required: true,
    },
    pincode: {
      type: Object,
      required: true,
    },
  },
  mounted() {
    this.convertGeoJson();
  },
  data() {
    return {
      show: true,
      zoom: 12,
      center: [12.9716, 77.5946],
      geojson: esri2geo(this.pincode, data => data),
      url: 'http://{s}.tile.osm.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      options: {
        onEachFeature,
      },
    };
  },
  watch: {
    mapType(val) {
      this.convertGeoJson(val);
    },
  },
  methods: {
    convertGeoJson(val) {
      const t = this;
      let dataToBeParsed = t.pincode;

      if (val === 'pincode') {
        dataToBeParsed = t.pincode;
      }
      if (val === 'locality') {
        dataToBeParsed = t.locality;
      }
      esri2geo(dataToBeParsed, (err, data) => {
        t.geojson = data;
      });
    },
  },
};
</script>

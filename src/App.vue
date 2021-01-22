<template>
  <div id="map"></div>
</template>

<script>
import maplibre from "maplibre-gl";
export default {
  name: "App",
  components: {},

  data() {
    return {
      token: process.env.VUE_APP_MAPCHERRY_API_KEY,
      style: "light",
      map: null,
    };
  },

  mounted() {
    this.map = this.createMap();
  },
  methods: {
    createMap() {
      const self = this;
      const map = new maplibre.Map({
        container: "map",
        style:
          "https://api.mapcherry.io/styles/" +
          self.style +
          ".json?key=" +
          self.token,
        center: [25.391, 46.031],
        zoom: 5,
        maxBounds: [
          [18.2201924985, 42.6884447292],
          [31.62654341, 49.2208812526],
        ],
        customAttribution:
          '<a href="https://mapcherry.io/?ref=copyright" target="_blank">&copy; Mapcherry</a> <a href="https://openmaptiles.org/" target="_blank">OpenMapTiles</a> <a href="https://www.openstreetmap.org/copyright" target="_blank">&copy; OpenStreetMap contributors</a>',
      });
      map.addControl(new maplibre.NavigationControl());
      map.addControl(new maplibre.FullscreenControl());
      map.style.map.on("load", function () {
        map.addSource("highways-ro", {
          type: "vector",
          tiles: [
            "https://a.tiles.mapcherry.io/highways-ro/{z}/{x}/{y}.pbf?key=" +
              self.token,
          ],
          minzoom: 5,
          maxzoom: 14,
        });

        map.addLayer({
          id: "highways-existing",
          type: "line",
          source: "highways-ro",
          "source-layer": "highways-ro",
          filter: ["all", ["==", "highway", "motorway"]],
          layout: {
            "line-join": "round",
            "line-cap": "round",
          },
          paint: {
            "line-color": "#02A702",
            "line-width": 3,
          },
        });

        map.addLayer({
          id: "highways-proposed",
          type: "line",
          source: "highways-ro",
          "source-layer": "highways-ro",
          filter: ["all", ["==", "highway", "proposed"]],
          layout: {
            "line-join": "round",
            "line-cap": "round",
          },
          paint: {
            "line-color": "#292929",
            "line-width": 3,
          },
        });

        map.addLayer({
          id: "highways-construction",
          type: "line",
          source: "highways-ro",
          "source-layer": "highways-ro",
          filter: ["all", ["==", "highway", "construction"]],
          layout: {
            "line-join": "round",
            "line-cap": "round",
          },
          paint: {
            "line-color": "#FE5502",
            "line-width": 3,
          },
        });

        map.addLayer({
          id: "highway_shield",
          type: "symbol",
          source: "highways-ro",
          "source-layer": "highways-ro",
          filter: ["has", "ref"],

          layout: {
            "icon-image": "default_3",
            "icon-rotation-alignment": "viewport",
            "symbol-placement": {
              base: 1,
              stops: [[5, "line"]],
            },
            "symbol-spacing": 500,
            "text-field": "{ref}",
            "text-font": ["Roboto Regular"],
            "text-offset": [0, 0.15],
            "text-rotation-alignment": "viewport",
            "text-size": 10,
            "icon-size": 0.8,
          },
        });
        // remove roadshields from motorways in original style
        map.setFilter("road_shield", [
          "all",
          ["<=", "ref_length", 6],
          ["!in", "network", "us-interstate", "us-highway", "us-state"],
          ["!in", "class", "motorway"],
        ]);
      });
      return map;
    },
  },
};
</script>

<style>
html,
body,
#app,
#map {
  margin: 0;
  width: 100%;
  height: 100%;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#map canvas {
  outline: none;
}
</style>

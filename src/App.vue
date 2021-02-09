<template>
  <div id="map"></div>

  <div id="legenda" class="legend">
    <div class="element">
      <span class="box existing"></span> <span class="label"> Deschis </span>
    </div>
    <div class="element">
      <span class="box construction"></span>
      <span class="label"> în execuție </span>
    </div>
    <div class="element">
      <span class="box proposed"></span>
      <span class="label"> în pregătire </span>
    </div>
    <div class="element">
      <span class="box opening-year">2021</span>
      <span class="label"> Dată estimată deschidere</span>
    </div>
  </div>
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
          '<a href="https://mapcherry.io/?ref=copyright" target="_blank">&copy; Mapcherry</a> <a href="https://openmaptiles.org/" target="_blank">&copy; OpenMapTiles</a> <a href="https://www.openstreetmap.org/copyright" target="_blank">&copy; OpenStreetMap contributors</a>',
      });
      map.addControl(new maplibre.NavigationControl());
      map.addControl(new maplibre.FullscreenControl());
      map.style.map.on("load", function () {
        map.addSource("highways-ro", {
          type: "vector",
          tiles: [
            "https://a.tiles.mapcherry.io/highways-ro/{z}/{x}/{y}.pbf?key=" +
              self.token,
            "https://b.tiles.mapcherry.io/highways-ro/{z}/{x}/{y}.pbf?key=" +
              self.token,
            "https://c.tiles.mapcherry.io/highways-ro/{z}/{x}/{y}.pbf?key=" +
              self.token,
          ],
          minzoom: 5,
          maxzoom: 14,
        });

        map.addLayer(
          {
            id: "highways-existing",
            type: "line",
            source: "highways-ro",
            "source-layer": "highways-ro",
            filter: ["all", ["in", "highway", "motorway", "motorway_link"]],
            layout: {
              "line-join": "round",
              "line-cap": "round",
            },
            paint: {
              "line-color": "#02A702",
              "line-width": 3,
            },
          },
          "country_3" // insert before layer `country_3` so highways are painted below labels
        );

        map.addLayer(
          {
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
          },
          "country_3" // insert before layer `country_3` so highways are painted below labels
        );

        map.addLayer(
          {
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
          },
          "country_3" // insert before layer `country_3` so highways are painted below labels
        );

        map.addLayer(
          {
            id: "highways-construction-opening-date",
            type: "symbol",
            source: "highways-ro",
            "source-layer": "highways-ro",
            filter: ["all", ["==", "highway", "construction"]],
            layout: {
              "text-field": "{opening_date}",
              "text-font": ["Roboto Medium"],
              "text-size": {
                stops: [
                  [6, 13],
                  [15, 10],
                ],
              },
              visibility: "visible",
              "text-allow-overlap": false,
              "text-ignore-placement": false,
              "text-letter-spacing": 0.01,
              "text-max-width": 4,
              "text-offset": {
                stops: [
                  [6, [1, -0.5]],
                  [15, [0, -2.5]],
                ],
              },
              "text-optional": false,
              "text-anchor": "left",
              "text-keep-upright": false,
              "text-line-height": 0,
              "text-padding": {
                stops: [
                  [6, 25],
                  [15, 20],
                ],
              },
              "symbol-avoid-edges": true,
              "symbol-placement": "point",
              "symbol-spacing": 100,
              "text-justify": "auto",
              "text-pitch-alignment": "viewport",
              "text-rotation-alignment": "viewport",
            },
            paint: {
              "text-color": "#1D8A7F",
            },
          },
          "country_3" // insert before layer `country_3` so highways are painted below labels
        );

        map.addLayer(
          {
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
          },
          "country_3" // insert before layer `country_3` so highways are painted below labels
        );

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
  color: #2c3e50;
}
#map canvas {
  outline: none;
}

.legend {
  background-color: #fff;
  border-radius: 3px;
  bottom: 30px;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
  font: 0.9rem "Helvetica Neue", Arial, Helvetica, sans-serif;
  padding: 10px;
  position: absolute;
  left: 10px;
  z-index: 1;
}
.legend .element {
  display: flex;
  align-items: center;
  height: 1.4rem;
}
.legend .label {
  flex: 1 1 auto;
  padding-left: 10px;
  font-weight: bold;
}
.legend .box {
  width: 32px;
  height: 1.1rem;
  display: inline-block;
}

.legend .opening-year {
  font-size: 1rem;
  font-weight: bold;
  color: #1d8a7f;
}
.legend .existing {
  background-color: #02a702;
}
.legend .construction {
  background-color: #fe5502;
}
.legend .proposed {
  background-color: #292929;
}
</style>

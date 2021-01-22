# Highways Romania Vector Tiles

Vector tiles containing existing, planned and in construction highways in Romania published on mapcherry.io.

The tiles are available under `https://a.tiles.mapcherry.io/highways-ro/{z}/{x}/{y}.pbf?key={token}`. To use them in your project you need to generate your own token on [https://mapcherry.io](https://mapcherry.io).

The tiles can be plotted on map or mobile using maplibre-gl or maplibre-gl-native library. In the current repository you can find the source code for web demo using maplibre-gl and vuejs. 

Live demo: https://mapcherry.github.io/highways-romania-vector-tiles/

## Tiles specification

[TileJSON specification](./tilejson.json)
```
"vector_layers": [
        {
            "id": "highways-ro",
            "description": "",
            "minzoom": 0,
            "maxzoom": 14,
            "fields": {
                "highway": "String",
                "name": "String",
                "opening_date": "String",
                "ref": "String"
            }
        }
    ]
```


## Project setup

```
npm install
```

Update `.env` to contain your generated token. 

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```


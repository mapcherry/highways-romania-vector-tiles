# Highways Romania Vector Tiles

Vector tiles containing existing, planned and in construction highways in Romania.

The tiles are available under `https://a.tiles.mapcherry.io/highways-ro/{z}/{x}/{y}.pbf?key={token}`. To use them in your project you need to generate your own token on [https://mapcherry.io](https://mapcherry.io). Update `.env` to contain your generated token. 


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


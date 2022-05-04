<template>
    <div id="map" class="map"></div>
    <label for="speed">
        Speed range:
        <input type="range" id="speed" min="10" max="999" step="10" v-model="value"/>
        <button @click="toggle">{{ text }}</button>
    </label>
</template>
<script>
import { defineComponent } from 'vue'
import Feature from 'ol/Feature'
import Map from 'ol/Map'
import Point from 'ol/geom/Point'
import Polyline from 'ol/format/Polyline'
import VectorSource from 'ol/source/Vector'
import View from 'ol/View'
import XYZ from 'ol/source/XYZ'
import { Circle as CircleStyle,
    Fill,
    Icon,
    Stroke,
    Style,
} from 'ol/style'
import { Tile as TileLayer, Vector as VectorLayer } from 'ol/layer'
import { getVectorContext } from 'ol/render'
import { click } from 'ol/events/condition'
import Select from 'ol/interaction/Select'
import { Draw } from 'ol/interaction'


import geom from '../assets/route.json'

const key = 'Get your own API key at https://www.maptiler.com/cloud'
const attributions =
'<a href="https://www.maptiler.com/copyright/" target="_blank">&copy; MapTiler</a> ' + 
'<a href="https://www.openstreetmap.org/copyright" target="_blank">&copy; OpenStreetMap contributors</a>'

const center = [-5639523.95, -3501274.52]


export default defineComponent({
    name: 'a-Map',
    data () {
        return {
            map: null,
            text: 'start animation',
            animating: false,
            lastTime: '',
            value: 0,
            vectorLayer: null,
            route: null,
            geoMarker: null,
            position: null,
            styles: null,
            distance: 0,
            select: null
        }
    },
    mounted () {
        this.map = new Map({
            target: 'map',
            view: new View({
                center,
                zoom: 10,
                minZoom: 2,
                maxZoom: 19
            }),
            layers: [
                new TileLayer({
                    source: new XYZ({
                        attributions,
                        url: 'https://api.maptiler.com/maps/hybrid/{z}/{x}/{y}.jpg?key=' + key,
                        tileSize: 512
                    })
                })
            ]
        })

        const polyline = geom.routes[0].geometry

        this.route = new Polyline({
            factor: 1e6
        }).readGeometry(polyline, {
            dataProjection: 'EPSG:4326',
            featureProjection: 'EPSG:3857'
        })

        const routeFeature = new Feature({
            type: 'route',
            geometry: this.route
        })

        const startMarker = new Feature({
            type: 'icon',
            geometry: new Point(this.route.getFirstCoordinate()),
        })
        const endMarker = new Feature({
            type: 'icon',
            geometry: new Point(this.route.getLastCoordinate())
        })
        this.position = startMarker.getGeometry().clone()
        this.geoMarker = new Feature({
            type: 'geoMarker',
            geometry: this.position
        })

        this.styles = {
            route: new Style({
                stroke: new Stroke({
                    width: 6,
                    color: [237, 212, 0, 0.8]
                })
            }),
            icon: new Style({
                image: new Icon({
                    anchor: [0.5, 1],
                    src: '../assets/icon.png'
                })
            }),
            geoMarker: new Style({
                image: new CircleStyle({
                    radius: 7,
                    fill: new Fill({ color: 'black' }),
                    stroke: new Stroke({
                        color: 'white',
                        width: 2
                    })
                })
            })
        }

        this.vectorLayer = new VectorLayer({
            source: new VectorSource({
                features: [routeFeature, this.geoMarker, startMarker, endMarker]
            }),
            style: feature => {
                return this.styles[feature.get('type')]
            }
        })

        const selectClick = new Select({
            condition: click,
            style: ''
        })

        this.map.addLayer(this.vectorLayer)
        this.map.addInteraction(selectClick)
        selectClick.on('select', e => {
            console.log(this.route)
            console.log(e)
var source = new VectorSource();
            const draw = new Draw({
                source,
                type: 'Point'
            })
            this.map.addInteraction(draw)
        })        
    },
    methods: {
        moveFeature(event) {
            const time = event.frameState.time
            const elapsedTime = time - this.lastTime
            this.distance = (this.distance + (this.value * elapsedTime) / 1e6) % 2
            this.lastTime = time

            const currentCoordinate = this.route.getCoordinateAt(this.distance > 1 ? 2 - this.distance : this.distance)
            this.position.setCoordinates(currentCoordinate)
            const vectorContext = getVectorContext(event)
            vectorContext.setStyle(this.styles.geoMarker)
            vectorContext.drawGeometry(this.position)
            this.map.render()
        },
        startAnimation() {
            this.animating = true
            this.lastTime = Date.now()
            this.text = 'Stop Animation'
            this.vectorLayer.on('postrender', this.moveFeature)
            this.geoMarker.setGeometry(null)
        },
        stopAnimation() {
            this.animating = false
            this.text = 'Start Animation'

            this.geoMarker.setGeometry(this.position)
            this.vectorLayer.un('postrender', this.moveFeature)
        },
        toggle() {
            if (this.animating) {
                this.stopAnimation()
            } else {
                this.startAnimation()
            }
        }
    }
})
</script>
<style lang="scss" scoped>
.map {
    height: 400px;
    width: 100%;
}
</style>
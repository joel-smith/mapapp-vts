<template>
    <div ref="map-root" style="width: 100vw; height: 100vh">
    </div>
</template>

<script>
import Feature from 'ol/Feature';
import { Point } from 'ol/geom';
import TileLayer from 'ol/layer/Tile';
import VectorLayer from 'ol/layer/Vector';
import Map from 'ol/Map';
import 'ol/ol.css';
import { useGeographic } from 'ol/proj';
import OSM from 'ol/source/OSM';
import Vector from 'ol/source/Vector';
import Icon from 'ol/style/Icon';
import Style from 'ol/style/Style';
import View from 'ol/View';

const VEHIKL_COORDINATES = [-80.528793, 43.512501];

export default {
    name: 'MapContainer',
    components: {},
    props: {},
    methods: {
        setIconStyle(iconSrc = "https://openlayers.org/en/v4.6.5/examples/data/icon.png") {
            return new Style({
                image: new Icon({
                    anchor: [0.5, 46],
                    anchorXUnits: "fraction",
                    anchorYUnits: "pixels",
                    src: iconSrc,
                }),
            });
        },

        createPointFeature(x = -80.528793, y = 43.512501) {
            const tmpcoord = [x, y];
            const point = new Point(tmpcoord, "XY");
            return new Feature({
                geometry: point,
            });
        },

        createCenteredMap(vectorLayerSource, x = 0, y = 0) {
            new Map({
                target: this.$refs['map-root'],
                layers: [
                    new TileLayer({
                        source: new OSM()
                    }),
                    new VectorLayer({
                        source: vectorLayerSource,
                    })
                ],
                view: new View({
                    zoom: 10,
                    center: [x, y],
                    constrainResolution: true
                }),
            })
        }
    },
    mounted() {
        useGeographic();
        const iconFeature = this.createPointFeature();
        iconFeature.setStyle(this.setIconStyle());

        const vectorSource = new Vector({
            features: [iconFeature],
        });

        this.createCenteredMap(vectorSource, VEHIKL_COORDINATES[0], VEHIKL_COORDINATES[1]);
    },
}
</script>
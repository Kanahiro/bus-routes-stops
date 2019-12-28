<template>
    <div class="mapPane"
        @dragover.prevent="dragover"
        @drop.prevent="drop">
        <l-map
            :zoom="zoom"
            :center="center"
            :preferCanvas="true"
        >
            <!--レイヤーコントロール-->
            <l-control-layers
                position="topright"
                :collapsed="false"
            ></l-control-layers>
            <l-control-scale
                position="bottomleft"
                :imperial="false"
                :metric="true"
            ></l-control-scale>
            <!--レイヤ設定-->
            <l-tile-layer
                v-for="tileProvider in tileProviders"
                :key="tileProvider.name"
                :name="tileProvider.name"
                :visible="tileProvider.visible"
                :url="tileProvider.url"
                :attribution="tileProvider.attribution"
            ></l-tile-layer>
            <l-geo-json
                v-for="feature in routes.features"
                :key="feature.N07_003"
                :name="feature.properties.N07_003"
                :geojson="feature"
                :options="routesOptions"
                :options-style="styleFunction"
                layer-type="overlay"
                @click="onRoutesClick"
            ></l-geo-json>
            <l-geo-json
                :geojson="selectedBusstops"
                :options="busstopsOptions"
                @click="onBusstopsClick"
            ></l-geo-json>
        </l-map>
    </div>
</template>

<script>
    import {
        LMap,
        LTileLayer,
        LControlLayers,
        LControlScale,
        LGeoJson,
    } from 'vue2-leaflet';

    import routes from "../assets/routes.json";
    import busstops from "../assets/busstops.json";

    export default {
        name: 'MapPane',
        components: {
            LMap,
            LTileLayer,
            LControlLayers,
            LControlScale,
            LGeoJson,
        },
        props:{
            center: Array,
            zoom: Number,
            geojsons:Array
        },
        data() {
            return {
                tileProviders: [
                    {
                        name: 'OpenStreetMap',
                        visible: true,
                        url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
                        attribution: '© <a href="http://osm.org/copyright">OpenStreetMap</a> contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>'
                    },
                ],
                routesOptions: {
                    onEachFeature: function(feature, layer) {
                        layer.options.smoothFactor = 2;
                        layer.options.style.color = "#" + Math.floor(Math.random() * 16777215).toString(16);
                        layer.bindTooltip(
                            "<div>" +
                            feature.properties.N07_003 +
                            "</div>",
                            { permanent: false, sticky: true }
                        );
                    }
                },
                busstopsOptions: {
                    onEachFeature: function(feature, layer) {
                        layer.bindTooltip(
                            "<div>" +
                            feature.properties.P11_001 +
                            "</div>",
                            { permanent: false, sticky: true }
                        );
                    }
                },
                routes:routes,
                busstops:busstops,
                selectedBusstops:{"type":"FeatureCollection",
                                "features":[]},
                weight:5
            }
        },
        methods: {
            dragover: function(event) {
                return event
            },
            drop: function(event) {
                let fileList = event.dataTransfer.files;
                let vm = this
                for ( let i = 0; i < fileList.length; i++ ) {
                    let reader=new FileReader()
                    reader.onload=function(e){
                        let geojson = JSON.parse(reader.result)
                        geojson.name = fileList[i].name
                        let geojsons = vm.geojsons.push(geojson)
                        vm.$emit("onGeojsonEditted", geojsons)
                    }
                    reader.readAsText(fileList[i])
                }
            },
            onRoutesClick: function (event) {
                let selectedBusstopsGeojson = {"type":"FeatureCollection",
                                            "features":[]
                                            }
                for ( let key in this.busstops.features ) {
                    if ( this.busstops.features[key].properties.P11_004_1.includes(event.layer.feature.properties.N07_003)) {
                        selectedBusstopsGeojson["features"].push(this.busstops.features[key])
                    }
                }
                this.selectedBusstops = selectedBusstopsGeojson
            },
            onBusstopsClick: function (event) {
                console.log(event.layer.feature)
            },
            onFeatureMouseover: function (event) {
            },
            onFeatureMouseout: function (event) {
                this.weight = 5
            },
        },
        computed: {
            styleFunction(event) {
                return {
                    weight: 5,
                }
            },
        }
    }
</script>

<style scoped>
    .mapPane {
        height: 600px;
        margin: 0;
        text-align: left;
    }
    .layer-style-control {
        background: #fff;
        padding: 0 0.5em;
        border: 1px solid #aaa;
        border-radius: 0.5em;
    }
</style>
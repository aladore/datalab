<template>
  <section class="col-xl-12 mt-1" style="height:450px; width: 100%;">
      <l-map ref="map" style="height:450px;" :zoom="10" :max-zoom="18" :min-zoom="5" :center="center">
        <l-control-layers position="bottomright"></l-control-layers>
        <l-tile-layer
          v-for="tileProvider in tileProviders"
          :key="tileProvider.name"
          :name="tileProvider.name"
          :visible="tileProvider.visible"
          :url="tileProvider.url"
          :attribution="tileProvider.attribution"
          layer-type="base"/>
        <l-control-scale position="topright" :imperial="false" :metric="true"></l-control-scale>
        <l-feature-group ref="features">
          <!--                      <l-popup><span> {{caller}}</span></l-popup>-->
        </l-feature-group>
        <l-marker
          @dragend="updateCoordinates"
          v-if="mutableLocation.lat && mutableLocation.lng"
          :lat-lng.sync="mutableLocation"
          :draggable="markerDraggable">
          <l-icon>
            <marker-icon
              :type="'marker'"
              :icon-anchor="staticAnchor"
              :markerColor="'#98242d'"
              :icon-size="32"
              :sliderDistance = sliderDistance.value
            />
          </l-icon>
        </l-marker>
        <l-circle
          v-if="mutableLocation.lat && mutableLocation.lng"
          :draggable="markerDraggable"
          :lat-lng.sync="mutableLocation"
          :radius="circle.radius"
          :color="circle.color"
          :fillColor="circle.fillColor"
          :stroke="circle.stroke"
          :fillOpacity="circle.fillOpacity"
          :weight=1
        />
        <l-geo-json v-if="geojson" :geojson="geojson" :options="geojsonOptions"></l-geo-json>
      </l-map>
  </section>
</template>

<script>
    import { OpenStreetMapProvider } from 'leaflet-geosearch';
    import 'leaflet/dist/leaflet.css';

    import {LMap, LMarker, LPopup, LTileLayer, LControlLayers, LTooltip, LCircleMarker, LControlScale, LFeatureGroup, LCircle, LIcon} from 'vue2-leaflet';
    import LGeoJson from "vue2-leaflet/src/components/LGeoJson";
    import LGeosearch from "vue2-leaflet-geosearch";
    import L from 'leaflet';
    import MarkerIcon from "./MarkerIcon";

    export default {
        name: "DataLabMap",
        props: {
            circle: {
                type: Object,
            },
            sliderDistance: {
                type: Object,
            },
            location: {
                type: Object,
            },
            center: {
                type: Array,
            },
            modalDistance: {
                type: Boolean,
            }
        },
        components: {
            LGeoJson,
            LMap,
            LTileLayer,
            LControlLayers,
            LMarker,
            LIcon,
            LTooltip,
            LPopup,
            LCircleMarker,
            LControlScale,
            LFeatureGroup,
            LCircle,
            MarkerIcon,
            LGeosearch,
        },
        data: function () {
            return {
                staticAnchor: [0, 0],
                iconSize: 34,
                url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
                tileProviders: [
                    {
                        name: 'OpenStreetMap',
                        visible: false,
                        attribution:
                            '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
                        url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
                    },
                    {
                        name: 'Voyager',
                        visible: true,
                        attribution:
                            '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
                        url: 'https://cartodb-basemaps-{s}.global.ssl.fastly.net/rastertiles/voyager/{z}/{x}/{y}.png',
                    },
                    {
                        name: 'Satellite',
                        visible: false,
                        attribution : 'Tiles &copy; Esri &mdash; Source: Esri, i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community',
                        url : 'https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',
                    },
                    {
                        name: 'OpenTopoMap',
                        visible: false,
                        url: 'https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png',
                        attribution: 'Map data: &copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>, <a href="http://viewfinderpanoramas.org">SRTM</a> | Map style: &copy; <a href="https://opentopomap.org">OpenTopoMap</a> (<a href="https://creativecommons.org/licenses/by-sa/3.0/">CC-BY-SA</a>)',
                    },
                ],
                markerDraggable: true,
                geojson: null,
                geojsonOptions: {
                    onEachFeature: this.onEachFeature
                },
            }
        },
        computed: {
            mutableLocation: {
                get: function() {
                    return this.location
                },
                set: function(value) {
                    this.$emit('on-update-coordinate', value)
                }
            }
        },
        mounted() {
            // this.getUserPosition();
        },
        methods: {
            updateCoordinates(coord) {
              this.$emit('on-update-coordinate', coord.target._latlng)
            },
        },
        watch: {
            modalDistance: function (modal) {
                if(modal) {
                    setTimeout(() => {
                        this.$refs.map.mapObject.invalidateSize();
                    }, 100);
                }
            },
        }
    }
</script>

<style scoped>

</style>

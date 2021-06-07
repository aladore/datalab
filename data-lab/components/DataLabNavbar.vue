<template>
  <!-- $MODAL -->
  <section style="top: 0;z-index: 999;">
    <div class="modal fade" data-backdrop="static" :class="[modalDistance ? 'in show' : '']" id="modalDistance" tabindex="-1" role="dialog" aria-labelledby="modalDistance" aria-hidden="true">
      <div class="modal-dialog modal-xxl" role="document">
        <div class="modal-content bg-terciary">
          <div class="modal-header">
            <div class="modal-title font-weight-bold text-lg" id="exampleModalLabel">Gestion de la distance</div>
            <div @click="modalDistance = !modalDistance" type="button" class="close" data-dismiss="modal" aria-label="Close">
              <i class="fa fa-close" aria-hidden="true"></i>
            </div>
          </div>
          <div class="modal-body">
            <div class="container-fluid">
              <div class="row">
                <div class="text-white font-weight-bold text-lg col-xl-8 offset-2 p-0 mb-5">
                  <div> Sélectionner une distance :</div>
                  <vue-slider
                    class="slider-rewrite p-3"
                    v-model="sliderDistance.value"
                    @change="updateDistance"
                    :lazy="true"
                    :marks="sliderDistance.marks"
                    :interval="5"
                    :min="20"
                    :max="300"
                    :adsorb="true"
                    :height="sliderDistance.height"
                    :dot-size="sliderDistance.height"
                    :dot-options="sliderDistance.dotOptions"
                    :tooltip-formatter="val => val+'km'"
                    :process="sliderDistance.process1"
                    :tooltip-style="sliderDistance.tooltip"
                  ></vue-slider>
                </div>
                <!-- $MAP -->
                <div class="col-xl-12 mt-1" style="height:450px; width: 100%;">
                  <DataLabMap @on-update-coordinate="onUpdateCoordinates" :modal-distance="modalDistance" :slider-distance="sliderDistance" :center="center" :location="location" :circle="circle"></DataLabMap>
                </div>
                <!-- END MAP -->
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- $NAVBAR -->
    <div class="row">
      <div class="col-xl-12 p-0">
        <nav class="navbar navbar-light bg-light">
          <a class="navbar-brand" href="#">
            <img src="https://s3-eu-west-1.amazonaws.com/statics.send-up.net/send-up-v2/build/images/logo-ocealia.svg" width="89" height="89" class="d-inline-block align-top mr-5" alt="Ocealia">
            <div class="text-terciary navbar-brand-label border-left pl-5">DATALAB</div>
          </a>
        </nav>
      </div>
    </div>
    <div class="dataLab-home-sticker">
      <a :key="links.accueil.route" :href="links.accueil.route" type="button" class="btn bg-terciary text-white px-4">
        <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Home.svg" width="31" height="31">
        <span class="ml-3">Accueil</span>
      </a>
    </div>
    <div class="dataLab-globalFilter" :class="[globalFilter ? '' : 'dataLab-globalFilter_hide']">
      <div class="card rounded-0 m-0">
        <div class="card-body bg-terciary p-4">
          <div class="mb-5">
            <div class="text-white text-lg col-xl-12 p-0 mb-2">Sélectionner une récolte :</div>

            <div class="d-flex">
              <select @change="changeCampaignYear" v-model="campaignYear" class="custom-select col-xl-7 col-lg-12 dataLab-select">
                <option v-for="option in campaignYearOptions" v-bind:value="option.value">
                  {{ option.text }}
                </option>
              </select>
            </div>
          </div>
          <div class="separator col-xl-12 my-4"></div>
          <div class="">
            <div class="text-white text-lg col-xl-12 p-0 mb-3">Sélectionner une distance :</div>
            <div class="w-100 d-flex align-items-center justify-content-between flex-wrap px-3">
              <div class="col-xl-7 col-lg-7 p-0 m-0">
                <vue-slider
                  class="slider-rewrite"
                  v-model="sliderDistance.value"
                  @change="updateDistance"
                  :lazy="true"
                  :marks="sliderDistance.marks"
                  :interval="5"
                  :min="20"
                  :max="300"
                  :adsorb="true"
                  :height="sliderDistance.height"
                  :dot-size="sliderDistance.height"
                  :dot-options="sliderDistance.dotOptions"
                  :tooltip-formatter="val => val+'km'"
                ></vue-slider>
              </div>
              <div @click="modalDistance= !modalDistance" data-toggle="modal" data-target="#modalDistance" class="text-center col-xl-4 col-lg-12 d-flex dataLab-globalFilter-map justify-content-end flex-column align-items-center">
                <div class="position-relative text-center dataLab-globalFilter-map-caption">
                  <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/MapLayout.png" width="77" height="77" class="rounded-circle" alt="map">
                  <i class="fa fa-map-marker text-white dataLab-globalFilter-map-caption-marker"></i>
                </div>
                <div class="text-secondary datalab-text-sm">Afficher la carte</div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <button @click="globalFilter = !globalFilter" type="button" class="btn btn_datalab py-3 bg-secondary text-white">{{globalFilter ? 'Masquer les filtres globaux' : 'Afficher les filtres globaux'}}</button>
    </div>
    <!-- END NAVBAR -->
  </section>
</template>

<script>
    import OcealiaPageMixin from "../OcealiaDataLabPageMixin";
    import DataLabMap from "./DataLabMap"
    import VueSlider from 'vue-slider-component';
    import {MultiSelect} from 'uiv'
    import 'vue-slider-component/theme/default.css';

    import * as Tools from "lodash";

    delete L.Icon.Default.prototype._getIconUrl;
    L.Icon.Default.mergeOptions({
        iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
        iconUrl: require('leaflet/dist/images/marker-icon.png'),
        shadowUrl: require('leaflet/dist/images/marker-shadow.png')
    });

    export default {
        name: "DataLabNavbar",
        components: {
            VueSlider,
            MultiSelect,
            DataLabMap
        },
        props: {
            argsHref : Array,
        },
        mixins: [OcealiaPageMixin],
        data() {
            return {
                classes: "ocealia-data-lab-widget",
                globalFilter: true,
                campaignYear: '2020',
                campaignYearOptions: [],
                species: null,
                modalDistance: false,
                sliderDistance: {
                    dotOptions: {
                        style: {
                            background: '#FCB316',
                            boxShadow: '-2px 0px 4px 0px rgba(0, 0, 0, 0.3)',
                        }
                    },
                    tooltip: {
                        style: {
                            background: '#FCB316',
                        }
                    },
                    process1: dotsPos => [[0, dotsPos[0],{ backgroundColor: '#FCB316' }]],
                    height: 15,
                    value: 20,
                    marks: {
                        '20': {
                            label: '20km',
                            style: {},
                            labelStyle: {
                                color: '#FCB316'
                            }
                        },
                        '100': {
                            label: '100km',
                            style: {},
                            labelStyle: {
                                color: '#FCB316'
                            }
                        },
                        '200': {
                            label: '200km',
                            style: {},
                            labelStyle: {
                                color: '#FCB316'
                            }
                        },
                        '300': {
                            label: '300km',
                            style: {},
                            labelStyle: {
                                color: '#FCB316'
                            }
                        },
                    },
                },
                links: {
                    'accueil' : {route: '#accueil/'+ this.argsHref[0]+'/'+this.argsHref[1]+'/'+this.argsHref[2]+'/'+this.argsHref[3], name: "Accueil"}
                },
                center: [47.313220, -1.319482],
                location: {
                    lat: suConfig.subscriber.location.latitude,
                    lng: suConfig.subscriber.location.longitude
                },
                address: '',
                circle: {
                    center: [suConfig.subscriber.location.latitude, suConfig.subscriber.location.longitude],
                    radius: 100000/2,
                    color: '#FCB316',
                    fillColor: '#FCB316',
                    fillOpacity: 0.4,
                },
            }
        },
        mounted() {
            this.campaignYear = this.argsHref[0];

            this.campaignYearOptions = []

            let configDate = new Date().getFullYear()-1;
            if(new Date().getMonth() < 7) {
                configDate = new Date().getFullYear()-1;
            } else {
                configDate = new Date().getFullYear();
            }
            let currentYear = configDate;

            for(let i = currentYear-4; i < currentYear+1; i++) {
                this.campaignYearOptions.push({ text: i , value: i  })
            }

            this.sliderDistance.value = parseInt(this.argsHref[1]);
            this.location = {
                lat: this.argsHref[2],
                lng: this.argsHref[3],
            };
            this.center = [this.argsHref[2], this.argsHref[3]];
            this.circle.radius = (parseInt(this.argsHref[1]) *1000)/2;
        },
        methods: {
            changeCampaignYear(e) {
                this.redirectUrl();
            },
            onUpdateCoordinates(coord) {
                this.location = {
                    lat: coord.lat,
                    lng: coord.lng,
                };
                this.redirectUrl();
            },
            updateDistance(e) {
                this.circle.radius = (this.sliderDistance.value *1000)/2;
                this.redirectUrl();
            },
            redirectUrl : Tools.debounce(function (e) {
                this.redirectTo([this.campaignYear.toString(), this.sliderDistance.value.toString(), this.location.lat.toString(), this.location.lng.toString()])
            },3000)
        },
        watch : {
            argsHref: function (newHref) {
                this.argsHref = newHref;
                if(this.argsHref.length) {
                    this.links.accueil = {route: '#accueil/'+ this.argsHref[0]+'/'+this.argsHref[1]+'/'+this.argsHref[2]+'/'+this.argsHref[3], name: "Accueil"}
                }
            }
        }
    }
</script>


<style lang="scss" scoped>
  @import "../style/theme";
  @import "../style/navbar";

  @media (max-width: 920px) {
    .dataLab-globalFilter {
      width: 50%;
    }
  }

  @media (max-width: 540px) {
    .dataLab-globalFilter {
      width: 100%;
    }
  }

  .modal-xxl {
    max-width: 1280px;
  }
  .modal {
    .modal-content{
      transform: translate(0, 10%);
    }
    .close {
      position: absolute;
      right: -5px;
      top: -13px;
      background-color: #fff;
      border-radius: 50%;
      opacity: 1;
      i {
        font-size: 15px;
        color: $terciary-color;
      }
    }
  }
</style>

<style lang="scss">
  .slider-rewrite,
  .vue-slider {
    //margin: 1rem;
    .vue-slider-rail {
      background: #fff;
    }
    .vue-slider-process {
      background-color: scale-color(#FCB316, $lightness: 21%) !important;
      &:hover {
        background-color: scale-color(#FCB316, $lightness: 21%) !important;
      }
    }
    .vue-slider-dot-handle {
      background-color: scale-color(#FCB316, $saturation: 34%) !important;
    }
    .vue-slider-dot-tooltip-inner {
      background: #FCB316;
      border-color: #FCB316;
    }
  }

</style>

<style>
  /* global styling */
  .leaflet-control-geosearch *,
  .leaflet-control-geosearch *:before,
  .leaflet-control-geosearch *:after {
    box-sizing: border-box;
  }

  /* leaflet button styling */
  .leaflet-control-geosearch .leaflet-bar-part {
    border-radius: 4px;
    border-bottom: none;
  }

  .leaflet-control-geosearch a.leaflet-bar-part:before,
  .leaflet-control-geosearch a.leaflet-bar-part:after {
    position: absolute;
    display: block;
    content: '';
  }

  /* magnifying glass */
  .leaflet-control-geosearch a.leaflet-bar-part:before {
    top: 19px;
    left: 16px;
    width: 8px;
    border-top: 2px solid #555;
    transform: rotateZ(45deg);
  }

  .leaflet-control-geosearch a.leaflet-bar-part:after {
    top: 6px;
    left: 6px;
    height: 14px;
    width: 14px;
    border-radius: 50%;
    border: 2px solid #555;
  }

  /* resets for pending and error icons */
  .leaflet-control-geosearch.error a.leaflet-bar-part:before,
  .leaflet-control-geosearch.pending a.leaflet-bar-part:before {
    display: none;
  }

  .leaflet-control-geosearch.pending a.leaflet-bar-part:after,
  .leaflet-control-geosearch.error a.leaflet-bar-part:after {
    left: 50%;
    top: 50%;
    width: 18px;
    height: 18px;
    margin: -9px 0 0 -9px;
    border-radius: 50%;
  }

  /* pending icon */
  .leaflet-control-geosearch.pending a.leaflet-bar-part:after {
    content: '';
    border: 2px solid #555;
    border-top: 2px solid #f3f3f3;
    animation: spin 1s linear infinite;
  }

  /* error icon */
  .leaflet-control-geosearch.error a.leaflet-bar-part:after {
    content: '!';
    line-height: initial;
    font-weight: 600;
    font-size: 18px;
    border: none;
  }

  /* search form styling */
  .leaflet-control-geosearch form {
    display: none;
    position: absolute;
    top: -2px;
    left: 28px;
    border-radius: 0 4px 4px 0;
    border: 2px solid rgba(0, 0, 0, 0.2);
    border-left: none;
    background-color: #fff;
    background-clip: padding-box;
    z-index: -1;
    height: auto;
    margin: 0;
    padding: 0 8px;
  }

  .leaflet-control-geosearch.active form {
    display: block;
  }

  .leaflet-control-geosearch form input {
    min-width: 200px;
    width: 100%;
    border: none;
    outline: none;
    margin: 0;
    padding: 0;
    font-size: 12px;
    height: 30px;
    border-radius: 0 4px 4px 0;
    text-indent: 8px;
    color: black;
  }

  .leaflet-control-geosearch .results {
    background: #fff;
  }

  .leaflet-control-geosearch .results > * {
    line-height: 24px;
    padding: 0 8px;
    border: 1px solid transparent;

    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .leaflet-control-geosearch .results.active {
    padding: 8px 0;
    border-top: 1px solid #c6c6c6;
  }

  .leaflet-control-geosearch .results > .active,
  .leaflet-control-geosearch .results > :hover {
    background-color: #f8f8f8;
    border-color: #c6c6c6;
    cursor: pointer;
    color: #0f0f0f;
  }

  /* add missing border to form */
  .leaflet-control-geosearch .results.active:after {
    content: '';
    display: block;
    width: 0;
    border-left: 2px solid rgba(0, 0, 0, .2);
    position: absolute;
    left: -2px;
    bottom: -2px;
    top: 30px;
  }

  /* animations */
  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }

  .leaflet-top .leaflet-control-geosearch.bar,
  .leaflet-bottom .leaflet-control-geosearch.bar {
    display: none;
  }

  .leaflet-control-geosearch.bar {
    position: relative;
    display: block;
    height: auto;
    width: 400px;
    margin: 10px auto 0;
    cursor: auto;
    z-index: 1000;
  }

  .leaflet-control-geosearch.bar form {
    position: relative;
    top: 0;
    left: 0;
    display: block;
    border: 2px solid rgba(0, 0, 0, 0.2);
    border-radius: 4px;
  }

  .leaflet-control-geosearch.bar form input {
    min-width: 100%;
    width: 100%;
  }

  .leaflet-control-geosearch.bar .results.active:after {
    opacity: .2;
  }

  .leaflet-right .leaflet-control-geosearch form {
    right: 28px;
    left: initial;
    border-radius: 4px 0 0 4px;
    border-left: inherit;
    border-right: none;
  }

  .leaflet-control-geosearch a.reset {
    color: black;
    position: absolute;
    line-height: 30px;
    padding: 0 8px;
    right: 0;
    top: 0;
    cursor: pointer;
    border: none;
  }

  .leaflet-control-geosearch a.reset:hover {
    background: #f5f5f5;
  }
</style>

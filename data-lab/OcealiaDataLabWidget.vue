<template>
    <div :class="classes" data-widget="OcealiaDataLab" data-widget-id="Ocealia/DataLab">

        <div v-if="isAuthenticated">
            <!-- $MODAL -->
            <data-lab-navbar :baseHref="currentRoute" :argsHref="argsHref" :top3Farming="top3Farming"></data-lab-navbar>
            <!-- $DATA PANELS -->
            <div v-if="currentRoute == '' || currentRoute == '#accueil' || currentRoute == '#'" :baseHref="currentRoute"
                 :argsHref="argsHref" class="container">
                <div class="row d-flex justify-content-center">
                    <div class="col-md-5">
                        <div class="card card_datalab card_account">
                            <div class="card-header bg-primary p-5">
                                <div class="card-header-title text-center">
                                    <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/User-1.svg" width="58px" height="58px"/>
                                    <div class="card-header-title card_account-username p-3">
                                        <span>{{user.subscriber.name}} </span>
                                        <span>({{user.subscriber.customId}})</span>
                                    </div>
                                    <div class="font-weight-normal pt-3 pb-1">
                                        <span>Sur mon exploitation en</span>
                                        <span>{{ this.argsHref[0] }}</span>
                                        <span>dans Océ@gri</span>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="row d-flex justify-content-center">
                    <div class="col-md-7">
                        <div class="card card_datalab card_account-data">
                            <div class="card-body pt-5 pb-4 d-flex justify-content-between">
<!--                                <div class="card_account-data-info text-center font-weight-bold text-secondary d-flex justify-content-center align-items-center" v-if="showAllYears">{{argsHref[0]}}</div>-->
                                <div class="card_account-data-info text-center">
                                    <div class="card-title m-0 text-secondary font-weight-bold" v-if="campaigns[argsHref[0]]">
                                        <animated-number
                                          :value="campaigns[argsHref[0]].userHectarsGlobal"
                                          :formatValue="val => val.toFixed(0).replace('.', ',')"
                                          :duration="800"
                                        />
                                    </div>
                                    <div class="card-text text-primary">ha de cultures</div>
                                </div>
                                <div class="card_account-data-info text-center">
                                    <div v-if="campaigns[argsHref[0]]" class="card-title m-0 text-secondary font-weight-bold">
                                        <animated-number
                                          :value="campaigns[argsHref[0]].interventions"
                                          :formatValue="val => val.toFixed(0).replace('.', ',')"
                                          :duration="800"
                                        />
                                    </div>
                                    <div class="card-text text-primary">Interventions</div>
                                </div>
                                <div class="card_account-data-info text-center">
                                    <div v-if="campaigns[argsHref[0]]" class="card-title m-0 text-secondary font-weight-bold">
                                        <animated-number
                                          :value="campaigns[argsHref[0]].counterSpecies"
                                          :formatValue="val => val.toFixed(0)"
                                          :duration="800"
                                        />
                                    </div>
                                    <div class="card-text text-primary">Cultures</div>
                                </div>
                            </div>
                        </div>
                        <div v-if="showAllYears" class="row col-md-12 d-flex justify-content-center mt-4 dataCampaigns">
                            <div v-if="campaign" v-for="(campaign,index) in campaigns" class="col-md-12">
                                <div v-if="campaign.userHectarsGlobal" class="card card_datalab card_account-data mt-3">
                                    <div class="card_account-data-info text-center font-weight-bold text-secondary d-flex justify-content-center align-items-center" v-if="showAllYears">{{campaign.year}}</div>
                                    <div class="card-body pt-2 pb-2 d-flex justify-content-between">
                                        <div class="card_account-data-info text-center">
                                            <div class="card-title m-0 text-secondary font-weight-bold">
                                                <animated-number
                                                  :value="campaign.userHectarsGlobal"
                                                  :formatValue="val => val.toFixed(0).replace('.', ',')"
                                                  :duration="800"
                                                />
                                            </div>
                                            <div class="card-text text-primary">ha de cultures</div>
                                        </div>
                                        <div class="card_account-data-info text-center">
                                            <div class="card-title m-0 text-secondary font-weight-bold">
                                                <animated-number
                                                  :value="campaign.interventions"
                                                  :formatValue="val => val.toFixed(0).replace('.', ',')"
                                                  :duration="800"
                                                />
                                            </div>
                                            <div class="card-text text-primary">Interventions</div>
                                        </div>
                                        <div class="card_account-data-info text-center">
                                            <div class="card-title m-0 text-secondary font-weight-bold">
                                                <animated-number
                                                  :value="campaign.counterSpecies"
                                                  :formatValue="val => val.toFixed(0)"
                                                  :duration="800"
                                                />
                                            </div>
                                            <div class="card-text text-primary">Cultures</div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <i class="fa fa-chevron-down toggleYears" @click="showAllYears = !showAllYears"></i>
                    </div>
                </div>

                <div class="row mt-5">
                    <data-lab-assolement :baseHref="currentRoute" :argsHref="argsHref" :top3Farming="top3Farming" :top3UserFarming="top3UserFarming" :top3AreaFarming="top3AreaFarming"></data-lab-assolement>
                    <data-lab-yield :baseHref="currentRoute" :argsHref="argsHref" :top3Farming="top3Farming" :top3UserFarming="top3UserFarming" :top3AreaFarming="top3AreaFarming"></data-lab-yield>
                    <data-lab-i-f-t :baseHref="currentRoute" :argsHref="argsHref" :farming="farming"></data-lab-i-f-t>
                    <data-lab-entry :baseHref="currentRoute" :argsHref="argsHref" :user="user" :farming="farming"></data-lab-entry>
                </div>
            </div>
            <component v-else-if :is="routes[currentRoute]" :baseHref="currentRoute" :argsHref="argsHref"  :topSpecieId="topSpecieId" :user="user" :farming="farming" :areaSpecies="areaSpecies" :userSpecies="userSpecies"></component>
        </div>
      <div>
      </div>
    </div>
</template>

<script>
    import SuTools from "@/vendor/utils/SuTools";
    import SuWidget from "@/webapp/vuejs/mixins/SuWidget";
    import SuWidgetApiInternal from "../../../../mixins/SuWidgetApiInternal";
    import { setup } from 'axios-cache-adapter'

    import AnimatedNumber from "animated-number-vue";
    import {Collapse} from 'uiv'

    import DataLabNavbar from "./components/DataLabNavbar";
    import DataLabAssolement from "./components/DataLabAssolement";
    import DataLabAssolementDetails from "./pages/DataLabAssolementDetails";
    import DataLabEntry from "./components/DataLabEntry";
    import DataLabEntryDetails from "./pages/DataLabEntryDetails";
    import DataLabYield from "./components/DataLabYield";
    import DataLabYieldDetails from "./pages/DataLabYieldDetails";
    import DataLabIFT from "./components/DataLabIFT";
    import DataLabIFTDetails from "./pages/DataLabIFTDetails";


    /*
<div class="container su-block-bgc su-site su-block-vue">
    <ocealia-data-lab-widget data-vue-widget></ocealia-data-lab-widget>
</div>
     */

    export default {
        name: "OcealiaDataLabWidget",
        mixins: [
            SuWidget,
            SuWidgetApiInternal
        ],
        props: {
            fbDataEncoded: {
                type: String,
                default: function () {
                    return SuTools.b64EncodeUnicode(JSON.stringify(
                      {
                      }
                    ));
                }
            },
        },
        components: {
            Collapse,
            DataLabAssolement,
            DataLabAssolementDetails,
            DataLabEntry,
            DataLabEntryDetails,
            DataLabYield,
            DataLabYieldDetails,
            DataLabIFT,
            DataLabIFTDetails,
            DataLabNavbar,
            AnimatedNumber,
        },
        data(){
            return {
                classes: "ocealia-data-lab-widget",
                currentRoute: "#accueil",
                baseUrl: 'https://api.send-up.net/internal/method',
                page: "",
                routes: {
                    "#accueil" : "",
                    "#assolement": "DataLabAssolementDetails",
                    "#entry": "DataLabEntryDetails",
                    "#IFT": "DataLabIFTDetails",
                    "#yield": "DataLabYieldDetails",
                },
                argsHref: [],
                isLoading : false,
                isAuthenticated: false,
                showAllYears: false,
                message: "",
                userData : [],
                userSpecies: [],
                userSpeciesUnique: [],
                areaSpeciesUnique: [],
                areaSpecies: [],
                userHectarsGlobal: 0,
                userHectars: 0,
                areaHectars: 0,
                interventions: 0,
                top3UserFarming: [],
                top3AreaFarming: [],
                top3Farming: {},
                farming: {},
                userCropInquire : [],
                userSpeciesInquire : [],
                areaSpeciesInquire : [],
                dataBySpecies : [],
                counterSpecies: 0,
                yearBegin: 0,
                userSpeciesUniqueId : [],
                topSpecieId : null,
                campaigns : {}
            }
        },
        created() {
            suConfig.datalab = {
                cfg: "aZKG0oonlarqlhMz17QesxgP592fa_xJW0uaDo4RU-7NLH6MYtcCrTn8s4GwExIaRMa236SYIUUFWyQ5rpkV2YWiLZe-PscVVuMOdTMybasrrLJ6EFJujf1TSC8yZc6Qu_8hOyVzTTkiS84AS4O7Pz41HSxCGcbLiqhjcK_W6c_XshTbrZgFCb4LOSx0Cldg_dlilRhGjKTEwzeV6OSsojiwfsf5kRck_w-rc4hdzEflh7UgLjKPDxNJzeNDYFzD0WxPs0NpN3AX4iYXSXiDayN8AgoYis-5mRlx9MJnFB4go8gpxkrA-PjKA6KZSp9ttZrowNJQk0ZgtPBb5w32S6-R3zVAS7ecfilExK1o3bePn0wPW1a5PgwVSgBhoaF0C0lUKUTXoCmfbMYGBANSa9y5cgd5jFzB2WqORzlHve_8ICK1FdAlEQ0x9jcLV_QRZS6j80wznRvBoM2U9uJxNFcrjXGhl7FTRDRRdCfWVixxQoeBqRZPF0_VcN9T_czmX",
                baseUrl: "https://api.send-up.net/internal/method"
            };
            this.init();
        },
        computed : {
            user() {
                return suConfig
            },
        },
        methods: {
            async init() {
                const apiInternal = new AxiosApiInternal({username: apiUsername, password: apiToken, cacheName: 'dataLab'});
                apiInternal.login().then(() => {
                    try {
                        this.isLoading = true;
                        this.isAuthenticated = true;
                    } catch (e) {
                        if(e?.status === 401){
                            this.message = "Erreur d'authentification";
                        }
                        else {
                            this.message = e?.data?.message || "Erreur d'authentification, veuillez réessayer plus tard";
                        }
                        this.isAuthenticated = false;
                    } finally {
                        this.isLoading = false;
                        window.addEventListener("hashchange", this.onRouteUpdate);
                        this.onRouteUpdate();
                    }
                });
            },
            onRouteUpdate() {
                let fullRoute = window.location.hash.split('/'),
                  route = fullRoute[0],
                  argsHref = fullRoute.slice(1);

                if (!Object.keys(this.routes).includes(route)) {
                    route = this.routes[0];
                }

                this.currentRoute = route;
                this.argsHref = argsHref;

                if(this.currentRoute == undefined) {
                    this.currentRoute = '#accueil'
                }
            },
            getSpecies(yearPicked) {
                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    "params": {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCropZoneArea",
                        "params": {
                            "customId": suConfig.subscriber.customId,
                            "CropYear": yearPicked
                        },
                    },
                    cache: {ignoreCache: true, maxAge: 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    if(this.argsHref[0] == yearPicked) {
                        this.userData = resp.data.result;
                    }

                    let sumArea = 0,
                      sumInterventions = 0;

                    resp.data.result.forEach(data => {
                        sumArea += parseInt(data.Sum);
                        sumInterventions += parseInt(data.Interventions);
                    });

                    // this.campaigns[yearPicked] =
                        let dataOfYear  = {
                            'year' : yearPicked,
                            'userHectarsGlobal' : Math.round(sumArea),
                            'interventions' : sumInterventions,
                            'counterSpecies' : resp.data.result.length
                        }

                    this.$set(this.campaigns, yearPicked, dataOfYear)

                    if(yearPicked == this.argsHref[0] && this.campaigns.length) {
                        this.campaigns.sort((a,b) => (a.year > b.year) ? 1 : -1)
                    }
                });
            },
            getCropZoneByCustomId() {
                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    "params": {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCustomId",
                        "params": {
                            "customId": suConfig.subscriber.customId,
                            "CropYear": this.argsHref[0]
                        },
                    },
                    cache: {ignoreCache: false, maxAge : 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    this.userSpecies = resp.data.result;

                    this.userSpecies.forEach(specie => {
                        if(specie.cropZone.yield != 0) {
                        }
                    })

                    let dataBySpecies = this.groupBy(this.userSpecies, 'cropSpeciesId');
                    let totalHectar = 0;
                    dataBySpecies.forEach(data => totalHectar += parseFloat(data.area));

                    this.topSpecieId = dataBySpecies.sort((a, b) => (parseFloat(a.area) < parseFloat(b.area)) ? 1 : -1)[0].cropSpeciesId;
                })
            },


            groupBy(myArray, group) {
                const arrayHashmap = myArray.reduce((obj, item) => {
                    if(obj[item[group]]) {
                        obj[item[group]].area += item.cropZone.area;

                        // if Data are valid and inquire
                        if(item.cropZone.isCorrectYield && item.cropZone.area) {
                            obj[item[group]].areaYieldInquire += item.cropZone.area;
                            obj[item[group]].yield += item.cropZone.yield * (item.cropZone.area);
                        }
                        if(!obj[item[group]].cropsId.includes(item.cropId)) {
                            obj[item[group]].cropsId.push(item.cropId);
                        }
                        if(!obj[item[group]].cropsTypeLabel.includes(item.cropTypeLabel)) {
                            item.cropsTypeLabel.push(item.cropTypeLabel)
                        }
                        if (item.cropZone.isCorrectYield && item.cropZone.isCorrectSowingDate && item.cropZone.sowingDate) {
                            obj[item[group]].dataInquire += 1;
                        }

                        obj[item[group]].counter ++;
                        obj[item[group]].intervention += item.cropZone.otherTypeOperationCount + item.cropZone.sowingAndPlantingOperationCount + item.cropZone.manureFertilizingOperationCount + item.cropZone.otherFertilizingOperationCount + item.cropZone.herbicideFertilizingOperationCount+ item.cropZone.fungicidegOperationCount + item.cropZone.insecticideOperationCount + item.cropZone.regulateurOperationCount + item.cropZone.harvestOperationCount + item.cropZone.supplyOperationCount;


                        // IFT
                        obj[item[group]].cropsInquire += item.cropZone.iftTotal;
                        if(item.cropZone.iftInsecticide) {
                            obj[item[group]].iftInsecticide.ift += item.cropZone.iftInsecticide * item.cropZone.area;
                            obj[item[group]].iftInsecticide.counter ++;
                            obj[item[group]].iftInsecticide.area += item.cropZone.area;
                        }
                        if(item.cropZone.iFtHerbicide) {
                            obj[item[group]].iftHerbicide.ift += item.cropZone.iFtHerbicide * item.cropZone.area;
                            obj[item[group]].iftHerbicide.counter ++;
                            obj[item[group]].iftHerbicide.area += item.cropZone.area;
                        }
                        if(item.cropZone.iftFungicide) {
                            obj[item[group]].iftFungicide.ift += item.cropZone.iftFungicide * item.cropZone.area;
                            obj[item[group]].iftFungicide.counter ++;
                            obj[item[group]].iftFungicide.area += item.cropZone.area;
                        }
                        if(item.cropZone.iftTotal) {
                            obj[item[group]].iftTotal.ift += item.cropZone.iftTotal * item.cropZone.area;
                            obj[item[group]].iftTotal.counter ++;
                            obj[item[group]].iftTotal.area += item.cropZone.area;
                        }
                    } else{
                        item.userHectar = this.userHectars;
                        item.areaHectar = this.areaHectars;
                        item.area = item.cropZone.area;
                        // if Data are valid and

                        item.areaYieldInquire = 0;
                        item.yield = 0;
                        if(item.cropZone.isCorrectYield && item.cropZone.area) {
                            item.areaYieldInquire = item.cropZone.area;
                            item.yield = item.cropZone.yield*(item.cropZone.area);
                        }
                        item.cropsInquire = item.cropZone.iftTotal;

                        //IFT
                        item.iftInsecticide = {
                            'ift' : 0,
                            'counter' : 0,
                            'area' : 0
                        };
                        if(item.cropZone.iftInsecticide) {
                            item.iftInsecticide.ift = item.cropZone.iftInsecticide * item.cropZone.area;
                            item.iftInsecticide.counter ++;
                            item.iftInsecticide.area = item.cropZone.area;
                        };

                        item.iftHerbicide = {
                            'ift' : 0,
                            'counter' : 0,
                            'area' : 0
                        };
                        if(item.cropZone.iFtHerbicide) {
                            item.iftHerbicide.ift = item.cropZone.iFtHerbicide * item.cropZone.area;
                            item.iftHerbicide.counter ++;
                            item.iftHerbicide.area = item.cropZone.area;
                        };

                        item.iftFungicide =  {
                            'ift' : 0,
                            'counter' : 0,
                            'area' : 0
                        };
                        if(item.cropZone.iftFungicide) {
                            item.iftFungicide.ift = item.cropZone.iftFungicide * item.cropZone.area;
                            item.iftFungicide.counter ++;
                            item.iftFungicide.area = item.cropZone.area;
                        }

                        item.iftTotal = {
                            'ift' : 0,
                            'counter' : 0,
                            'area' : 0
                        }
                        if(item.cropZone.iftTotal != 0) {
                            item.iftTotal.ift = item.cropZone.iftTotal * item.cropZone.area;
                            item.iftTotal.counter ++;
                            item.iftTotal.area = item.cropZone.area;
                        }


                        item.cropsId = [item.cropId];
                        item.cropsTypeLabel = [item.cropTypeLabel];
                        item.intervention = item.cropZone.otherTypeOperationCount + item.cropZone.sowingAndPlantingOperationCount + item.cropZone.manureFertilizingOperationCount + item.cropZone.otherFertilizingOperationCount + item.cropZone.herbicideFertilizingOperationCount+ item.cropZone.fungicidegOperationCount + item.cropZone.insecticideOperationCount + item.cropZone.regulateurOperationCount + item.cropZone.harvestOperationCount + item.cropZone.supplyOperationCount;
                        item.dataInquire = 0;
                        if (item.cropZone.isCorrectYield && item.cropZone.isCorrectSowingDate && item.cropZone.sowingDate) {
                            item.dataInquire = 1;
                        }
                        item.counter = 0;

                        (obj[item[group]] = { ...item });
                    }
                    return obj;
                }, {});
                return Object.values(arrayHashmap);
            },
        },
        watch: {
            argsHref: function (newVal, oldVal) {
                if(!oldVal.length && !this.argsHref.length) {
                    let configDate = new Date().getFullYear()-1;
                    if(new Date().getMonth() < 7) {
                        configDate = new Date().getFullYear()-1;
                    } else {
                        configDate = new Date().getFullYear();
                    }
                    this.argsHref = [configDate, 20, suConfig.subscriber.location.latitude, suConfig.subscriber.location.longitude];
                } else {
                    this.getCropZoneByCustomId();
                    let currentYear = parseInt(this.argsHref[0]);
                    for(let i = currentYear-4; i < currentYear+1; i++) {
                        this.getSpecies(i)
                    }
                }
            }
        }
    }
</script>

<style lang="scss" scoped>
    @import "./style/theme";
    .toggleYears {
        position: absolute;
        bottom: -8px;
        left: 48%;
        background: #fff;
        padding: 8px;
        border-radius: 50%;
        cursor: pointer;
    }
  .dataCampaigns {
    background: #3d7b60;
    position: absolute;
    border-radius: 5px;
    z-index: 9999;
  }
</style>




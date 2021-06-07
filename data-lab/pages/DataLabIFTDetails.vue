<template>
  <section class="container">
    <div class="row mt-5">
      <div class="col-md-12">
        <div class="DataLab-panelFilter mt-5 bg-secondary d-flex justify-content-center flex-column">
          <div class="DataLab-panelFilter-button d-flex justify-content-center align-items-center p-4">
            <div class="text-lg mr-3 text-dark">Sélectionner les cultures à afficher :</div>
            <multi-select @change="changeSpecie"  class="multiselect_rewrite" placeholder="Espèce" v-model="specieSelected" :options="options" :limit="1" size="md"/>
          </div>
        </div>
      </div>
    </div>

    <div class="row">
      <div class="col-md-12">
        <div class="card card_datalab">
          <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
            <div class="card-header-title">IFT</div>
          </div>
          <div class="card-body d-flex align-items-center">
            <div class="col-md-6">
              <div class="dataLab-panel-IFT rounded-top p-2 py-4 d-flex justify-content-around align-items-center">
              <div class="dataLab-panel-IFT-caption text-center">
                <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/User-2.svg" width="48px" height="48px">
                <div class="text-primary">Moi</div>
              </div>
              <div class="dataLab-panel-IFT-data text-center">
                <div class="card-title m-0 text-secondary font-weight-bold">
                  <animated-number
                    v-if="ift.user.cropsEntry"
                    :value="ift.user.cropsEntry"
                    :formatValue="val => val.toFixed(0)"
                    :duration="800"
                  />
                  <div v-else class="text-secondary font-weight-bold">0</div>
                </div>
                <div class="card-text text-primary dataLab-IFT-plot-text">Parcelles renseignées</div>
              </div>
              <div class="dataLab-panel-IFT-data text-center">
                <div class="card-title m-0 text-secondary font-weight-bold">
                  <animated-number
                    v-if="ift.user.iftTotal"
                    :value="ift.user.iftTotal"
                    :formatValue="val => val.toFixed(1).replace('.', ',')"
                    :duration="800"
                  />
                  <div v-else class="text-secondary font-weight-bold">0</div>
                </div>
                <div class="card-text text-primary">IFT TOTAL</div>
              </div>
            </div>
              <div class="separator separator_IFT bg-primary"></div>
              <div class="dataLab-panel-IFT rounded-bottom py-2 py-4 d-flex justify-content-around align-items-center">
                <div class="dataLab-panel-IFT-data text-center">
                  <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Datalab-herbicide.svg" width="48px" height="48px"/>

                  <div class="card-title m-0 text-secondary font-weight-bold">
                    <animated-number
                      v-if="ift.user.iftHerbicide"
                      :value="ift.user.iftHerbicide"
                      :formatValue="val => val.toFixed(1).replace('.', ',')"
                      :duration="800"
                    />
                    <div class="text-secondary font-weight-bold" v-else>0</div>
                  </div>
                  <div class="card-text">Herbicide</div>
                </div>
                <div class="dataLab-panel-IFT-data text-center">
                  <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Datalab-Fungicide.svg" width="48px" height="48px"/>
                  <div class="card-title m-0 text-secondary font-weight-bold">
                    <animated-number
                      v-if="ift.user.iftFungicide"
                      :value="ift.user.iftFungicide"
                      :formatValue="val => val.toFixed(1).replace('.', ',')"
                      :duration="800"
                    />
                    <div class="text-secondary font-weight-bold" v-else>0</div>
                  </div>
                  <div class="card-text dataLab-IFT-plot-text">Fongicide</div>
                </div>
                <div class="dataLab-panel-IFT-data text-center">
                  <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Datalab-Insecticide.svg" width="48px" height="48px"/>
                  <div class="card-title m-0 text-secondary font-weight-bold">
                    <animated-number
                      v-if="ift.user.iftInsecticide"
                      :value="ift.user.iftInsecticide"
                      :formatValue="val => val.toFixed(1).replace('.', ',')"
                      :duration="800"
                    />
                    <div class="text-secondary font-weight-bold" v-else>0</div>
                  </div>
                  <div class="card-text">Insecticide</div>
                </div>
              </div>
            </div>
            <div class="col-md-6">
              <div class="dataLab-panel-IFT rounded-top p-2 py-4 d-flex justify-content-around align-items-center">
                <div class="dataLab-panel-IFT-caption text-center">
                  <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Tractor.svg" width="48px" height="48px">
                  <div class="text-primary">Coopérative</div>
                </div>
                <div class="dataLab-panel-IFT-data text-center">
                  <div class="card-title m-0 text-secondary font-weight-bold">
                    <animated-number
                      v-if="ift.area.cropsEntry"
                      :value="ift.area.cropsEntry"
                      :formatValue="val => val.toFixed(0).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ')"
                      :duration="800"
                    />
                    <div v-else class="text-secondary font-weight-bold">0</div>
                  </div>
                  <div class="card-text text-primary dataLab-IFT-plot-text">Parcelles renseignées</div>
                </div>
                <div class="dataLab-panel-IFT-data text-center">
                  <div class="card-title m-0 text-secondary font-weight-bold">
                    <animated-number
                      v-if="ift.area.iftTotal"
                      :value="ift.area.iftTotal"
                      :formatValue="val => val.toFixed(1).replace('.', ',')"
                      :duration="800"
                    />
                    <div v-else class="text-md font-weight-bold">0</div>
                  </div>
                  <div class="card-text text-primary">IFT TOTAL</div>
                </div>
              </div>
              <div class="separator separator_IFT bg-primary"></div>
              <div class="dataLab-panel-IFT rounded-bottom py-2 py-4 d-flex justify-content-around align-items-center">
                <div class="dataLab-panel-IFT-data text-center">
                  <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Datalab-herbicide.svg" width="48px" height="48px"/>
                  <div class="card-title m-0 text-secondary font-weight-bold">
                    <animated-number
                      v-if="ift.area.iftHerbicide"
                      :value="ift.area.iftHerbicide"
                      :formatValue="val => val.toFixed(1).replace('.', ',')"
                      :duration="800"
                    />
                    <div class="text-secondary font-weight-bold" v-else>0</div>
                  </div>
                  <div class="card-text">Herbicide</div>
                </div>
                <div class="dataLab-panel-IFT-data text-center">
                  <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Datalab-Fungicide.svg" width="48px" height="48px"/>

                  <div class="card-title m-0 text-secondary font-weight-bold">
                    <animated-number
                      v-if="ift.area.iftFungicide"
                      :value="ift.area.iftFungicide"
                      :formatValue="val => val.toFixed(1).replace('.', ',')"
                      :duration="800"
                    />
                    <div class="text-secondary font-weight-bold" v-else>0</div>
                  </div>
                  <div class="card-text dataLab-IFT-plot-text">Fongicide</div>
                </div>
                <div class="dataLab-panel-IFT-data text-center">
                  <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Datalab-Insecticide.svg" width="48px" height="48px"/>

                  <div class="card-title m-0 text-secondary font-weight-bold">
                    <animated-number
                      v-if="ift.area.iftInsecticide"
                      :value="ift.area.iftInsecticide"
                      :formatValue="val => val.toFixed(1).replace('.', ',')"
                      :duration="800"
                    />
                    <div class="text-secondary font-weight-bold" v-else>0</div>
                  </div>
                  <div class="card-text">Insecticide</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div v-if="chartOptionsIft.iftTotal && chartOptionsIft.iftTotal.series[0].data[0]" class="row">
      <div v-if="chart.series[0].data[0]" class="col-md-3" v-for="(chart, val) in chartOptionsIft" :key="`chartTonnes-${val}`">
      <div class="card card_datalab">
        <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
          <div class="card-header-title">{{chart.title.label}}</div>
        </div>
        <div class="card-body">
          <div class="sumApport">{{parseFloat(chart.title.iftCounter).toFixed(0).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ')}} données IFT</div>
          <highcharts style="height: 239px" class="dataLabAssolement" :options="chart" :ref="`chartPosition-${val}`"></highcharts>
        </div>
      </div>
    </div>
    </div>
    <div class="card card_datalab" v-else>
      <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
        L'échantillon de référence n'a pas saisie d'intervention
      </div>
    </div>
  </section>
</template>
<script>
    import {MultiSelect} from 'uiv'
    import AnimatedNumber from "animated-number-vue";
    import {Chart} from 'highcharts-vue'



    export default {
        name: "DataLabIFTDetails",
        components: {
            MultiSelect,
            AnimatedNumber,
            highcharts: Chart
        },
        props: {
            topSpecieId: Number,
            argsHref: Array,
        },
        data() {
            return {
                skeletonChartPosition: {
                    chart: {
                        type: 'column'
                    },
                    colors: ['#A5D6A7', '#FFC300', '#FF5733', '#C70039', '#900C3F', '#581845'],
                    title: {
                        text: 'Nothing',
                        style: {
                            fontSize: '14px',
                            fontWeight: '300',
                            left: 0,
                            color: '#fff',
                            float: true,
                        },
                    },
                    tooltip: {
                        backgroundColor: '#fff',
                        headerFormat: '<span style="font-size:10px"></span><table>',
                        shared: false,
                        useHTML: true
                    },
                    xAxis: {
                        categories: [],
                        crosshair: true,
                        title: {
                            // text: 'ift'
                        },
                        labels: {
                            x: 0
                        }
                    },
                    yAxis: {
                        categories: [0],
                        min: 0,
                        title: {
                            align: 'high',
                            offset: 0,
                            text: '% parcelles',
                            rotation: 0,
                            y: -20
                        },
                    },
                    legend: {
                        // useHTML: true,
                        enabled: false,
                        align: 'right',
                        x: 0,
                        y: 50,
                        verticalAlign: 'top',
                        layout: 'vertical',
                        // floating: true,
                        shadow: false,
                        // symbolWidth: 0,
                    },
                    plotOptions: {
                        series: {
                            allowPointSelect: true,
                            marker: {
                                enabled: true,
                                symbol: 'url(https://www.highcharts.com/samples/graphics/sun.png)'
                            },
                        }
                    },
                    series: []
                },
                options: [],
                chartOptionsIft: {},
                specieSelected : [],
                translation : {
                    iftTotal : 'IFT total',
                    iftHerbicide : 'IFT herbicide',
                    iftFungicide : 'IFT fongicide',
                    iftInsecticide : 'IFT insecticide',
                },
                ift : {
                    user : {
                        iftHerbicide : 0,
                        iftFungicide : 0,
                        iftInsecticide : 0,
                        iftTotal: 0,
                        cropsEntry: 0,
                    },
                    area : {
                        iftHerbicide : 0,
                        iftHerbicideCounter : 0,
                        iftFungicide : 0,
                        iftFungicideCounter : 0,
                        iftInsecticide : 0,
                        iftInsecticideCounter: 0,
                        iftTotal: 0,
                        cropsEntry: 0,
                    }
                },
                userSpecies : [],
                countIFT : 0,
            }
        },
        mounted() {
            this.getCropZoneByCustomId();
        },
        methods : {
            async changeSpecie(specie) {
                this.options = [],
                this.specieSelected = [specie[0]];
                this.chartOptionsIft = {};
                this.ift.user.iftHerbicide = 0;
                this.ift.user.iftFungicide = 0;
                this.ift.user.iftInsecticide = 0;
                this.ift.user.iftTotal = 0;
                this.ift.user.cropsEntry = 0;
                this.ift.area.iftHerbicide = 0;
                this.ift.area.iftFungicide = 0;
                this.ift.area.iftInsecticide = 0;
                this.ift.area.iftTotal = 0;
                this.ift.area.cropsEntry = 0;

                this.getCropZoneByCustomId();
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
                        debug : true,
                    },
                    cache: {ignoreCache: false, maxAge : 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    this.userSpecies = resp.data.result;
                    this.filterUserSpecies();
                });
            },
            async filterUserSpecies() {
                let speciesAvaiable = [];
                this.userSpecies.forEach(specie => {
                    speciesAvaiable.push(specie.cropSpeciesId)
                });


                let filterSeason = this.groupBy(this.userSpecies, 'cropSeason');

                filterSeason = filterSeason.sort((a, b) => (a.cropSeasonLabel < b.cropSeasonLabel) ? 1 : -1);
                filterSeason.forEach(season => {
                    season.cropSeasonSpecies.label.forEach((label,index) => {
                        if(speciesAvaiable.includes(season.cropSeasonSpecies.value[index])) {
                            this.options.push({
                                value: season.cropSeasonSpecies.value[index],
                                label: label,
                                group: season.cropSeasonLabel,
                                seasonId: season.cropSeasonId,
                            });
                        }
                    });
                });

                this.options = this.options.sort((a, b) => (a.label > b.label) ? 1 : -1);

                if(!this.specieSelected.length && this.topSpecieId) {
                    this.specieSelected = [this.topSpecieId];
                }
                // else {
                //     this.specieSelected = [this.options[0].value];
                // }
                await this.getIFT('area', 'iftTotal');
                await this.getIFT('area', 'iftFungicide');
                await this.getIFT('area', 'iftHerbicide');
                await this.getIFT('area', 'iftInsecticide');

                let totalArea = 0, insecticide = 0, fungicide = 0, herbicide = 0, counter = 0, iftTot = 0;
                let dataSelected = [];
                this.userSpecies.forEach(specie => {
                    if(specie.cropSpeciesId == this.specieSelected[0]) {
                        herbicide += specie.cropZone.iftHerbicide * specie.cropZone.area ;
                        fungicide += specie.cropZone.iftFungicide * specie.cropZone.area;
                        insecticide += specie.cropZone.iftInsecticide * specie.cropZone.area;
                        totalArea += specie.cropZone.area;
                        iftTot += specie.cropZone.iftTotal * specie.cropZone.area;;
                        if(specie.cropZone.iftTotal) {
                            counter ++
                        }
                    }
                    dataSelected.push(specie)
                });

                this.ift.user.iftHerbicide = herbicide / totalArea;
                this.ift.user.iftFungicide = fungicide / totalArea;
                this.ift.user.iftInsecticide = insecticide / totalArea;
                this.ift.user.iftTotal = iftTot / totalArea;
                this.ift.user.cropsEntry = counter;

                this.getIftPosition('area', 'iftTotal');
            },
            async getIFT(by, type) {
                let params = {};
                params.CropYear = parseInt(this.argsHref[0]);
                params.Type = type;
                params.SpeciesId = this.specieSelected[0];

                if (by == 'user') {
                    params.customId = suConfig.subscriber.customId;
                }
                return axiosSuApi.get(suConfig.datalab.baseUrl, {
                    params: {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCropZoneIft",
                        params,
                    },
                    cache: {ignoreCache: true}
                }).then((resp) => {

                    if(!this.countIFT && resp.data.result.length) {
                        this.countIFT = resp.data.result[0].AreaCount
                    }
                    resp.data.result.forEach(data => {
                        if(type == 'iftTotal') {
                            this.ift[by].iftTotal += parseFloat(data.Total);
                        }

                        this.ift[by][type+'Counter'] = resp.data.result[0].AreaCount;
                        this.$set(this.ift[by], type, parseFloat(data.Total));
                        if(this.ift[by].cropsEntry < parseFloat(data.AreaCount)) {
                            this.ift[by].cropsEntry = parseFloat(data.AreaCount);
                        }
                    });
                });

            },
            getIftPosition(by, element) {
                let params = {};

                params.CropYear = parseInt(this.argsHref[0]);
                params.SpeciesId = this.specieSelected;
                params.Type = element;

                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    "params": {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCropZoneIftRange",
                        params
                    },
                    cache: {ignoreCache: true, maxAge: 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    let res = resp.data.result;
                    let sumIft = 0;
                    let maxPercentage = 0;

                    res.forEach(ift => {
                        sumIft += parseFloat(ift.Count);
                    });

                    res.forEach(ift=> {
                        ift.percentage = parseInt(ift.Count) / sumIft * 100;
                        if(ift.percentage > maxPercentage) {
                            maxPercentage = ift.percentage;
                        }
                    });

                    let dataAreaSorted = res.sort((a, b) => (parseFloat(a.Yield) > parseFloat(b.Yield)) ? 1 : -1);
                    this.generateChartIftPos(dataAreaSorted, element, maxPercentage);
                });
            },
            generateChartIftPos(areaData ,element, maxPercentage) {
                let label = this.translation[element];
                const chart = JSON.parse(JSON.stringify(this.skeletonChartPosition));

                let labelSpecie = this.options.filter(option => option.value == this.specieSelected[0])[0].label;

                chart.title.label = 'Mon ' + this.translation[element] + ' comparé au territoire' + ' - '+ labelSpecie +' - '+ this.argsHref[0];

                chart.title.iftCounter = this.ift.area[element+'Counter'];
                chart.series = [
                    {
                        name: "Mon territoire",
                        data: [],
                        color: 'rgb(255, 195, 0)',
                        pointWidth: 5,
                        tooltip: {
                            backgroundColor: '#fff',
                            pointFormatter: function(el) {
                                return '<div>'+ label +': <strong>'+this.category.toString().replace('.', ',')+'</strong></div>' +
                                    '<div> % de parcelles : <strong>'+this.y.toFixed(1).replace('.', ',') +'</strong></div>'
                            }
                        }
                    },
                    {
                        dataLabels: {
                            enabled: true,
                            style: {
                                color: '#A5D6A7'
                            },
                            useHTML: true,
                            formatter: function () {
                                return '<div class="myPins">Moi</div>';
                            }
                        },
                        enableMouseTracking: true,
                        name: "Mon exploitation",
                        marker: {
                            symbol: 'diamond'
                        },
                        data: [],
                        // yAxis: 1,
                        pointWidth: 1,
                        color: 'rgb(165, 214, 167)',
                        tooltip: {
                            backgroundColor: '#fff',
                            pointFormatter: function (el) {
                                return '<div>'+ label +' : <strong>' + this.ift.toFixed(1).replace('.', ',') + '</strong></div>'
                            }
                        }
                    }
                ];

                let lastAreaYield = 0;

                areaData.forEach((data, index) => {
                    chart.xAxis.categories.push(parseFloat(data.Yield).toFixed(1).replace('.', ','));
                    chart.series[0].data.push(parseFloat(data.percentage));
                    if(this.ift.user[element] == 0) {
                        chart.series[1].data.push(
                            {
                                ift: 0,
                            }
                        )
                    }
                    else if(this.ift.user[element] >= lastAreaYield && this.ift.user[element] < parseFloat(data.Yield)) {
                        chart.series[1].data.push(
                            {
                              y: maxPercentage,
                              ift: parseFloat(this.ift.user[element]),
                              marker : {
                                  symbol: 'url(https://www.highcharts.com/samples/graphics/sun.png)'
                              }}
                        );
                    } else {
                        if(index != 0) {
                            chart.series[1].data.push('');
                        }
                    }
                    if (areaData.length -1 == index && this.ift.user[element] > parseFloat(data.Yield)) {
                        chart.xAxis.categories.push(this.ift.user[element].toFixed(1).replace('.', ','));
                        chart.series[0].data.push({
                            ift: 0,
                        });
                        chart.series[1].data.push(
                            {
                                ift: 0,
                            },
                            {
                                y: maxPercentage,
                                ift: parseFloat(this.ift.user[element]),
                                marker : {
                                    symbol: 'url(https://www.highcharts.com/samples/graphics/sun.png)'
                                }
                            },
                        );
                    }
                    lastAreaYield = parseFloat(data.Yield)
                });

                this.$set(this.chartOptionsIft, element, chart);

                if(element == 'iftTotal') {
                    this.getIftPosition('area', 'iftHerbicide');
                }
                if(element == 'iftHerbicide') {
                    this.getIftPosition('area', 'iftFungicide');
                }
                if(element == 'iftFungicide') {
                    this.getIftPosition('area', 'iftInsecticide');
                }
            },
            groupBy(myArray, group) {
                const arrayHashmap = myArray.reduce((obj, item) => {

                    let groupBy = Math.round(item.cropZone[group]);
                    if(obj.hasOwnProperty(groupBy)) {
                        obj[groupBy].area += item.cropZone.area;

                        //Specie season
                        obj[groupBy].cropSeason = item.cropZone.cropSeason;
                        if(!obj[groupBy].cropSeasonSpecies.value.includes(item.cropSpeciesId)) {
                            obj[groupBy].cropSeasonSpecies.value.push(item.cropSpeciesId);
                        }
                        if(!obj[groupBy].cropSeasonSpecies.label.includes(item.cropSpeciesLabel)) {
                            obj[groupBy].cropSeasonSpecies.label.push(item.cropSpeciesLabel);
                        }

                        // IFT
                        if(item.cropZone.iftInsecticide) {
                            obj[groupBy].iftInsecticide.ift += item.cropZone.iftInsecticide * item.cropZone.area;
                            obj[groupBy].iftInsecticide.counter ++;
                            obj[groupBy].iftInsecticide.area += item.cropZone.area;
                        }
                        if(item.cropZone.iFtHerbicide) {
                            obj[groupBy].iftHerbicide.ift += item.cropZone.iFtHerbicide * item.cropZone.area;
                            obj[groupBy].iftHerbicide.counter ++;
                            obj[groupBy].iftHerbicide.area += item.cropZone.area;
                        }
                        if(item.cropZone.iftFungicide) {
                            obj[groupBy].iftFungicide.ift += item.cropZone.iftFungicide * item.cropZone.area;
                            obj[groupBy].iftFungicide.counter ++;
                            obj[groupBy].iftFungicide.area += item.cropZone.area;
                        }
                        if(item.cropZone.iftTotal) {
                            obj[groupBy].iftTotal.ift += item.cropZone.iftTotal * item.cropZone.area;
                            obj[groupBy].iftTotal.counter ++;
                            obj[groupBy].iftTotal.area += item.cropZone.area;
                        }
                    } else{
                        item.area = item.cropZone.area;
                        item.cropSeason = item.cropZone.cropSeason;
                        item.cropSeasonSpecies = {
                            value : [item.cropSpeciesId],
                            label : [item.cropSpeciesLabel]
                        }
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

                        obj[groupBy] = item;
                    }
                    return obj;
                }, {});
                return Object.values(arrayHashmap);
            },
        },
        watch : {
            argsHref: function (newFarm) {
                this.options = [];
                this.chartOptionsIft = {};
                this.ift.user.iftHerbicide = 0;
                this.ift.user.iftFungicide = 0;
                this.ift.user.iftInsecticide = 0;
                this.ift.user.iftTotal = 0;
                this.ift.user.cropsEntry = 0;
                this.ift.area.iftHerbicide = 0;
                this.ift.area.iftFungicide = 0;
                this.ift.area.iftInsecticide = 0;
                this.ift.area.iftTotal = 0;
                this.ift.area.cropsEntry = 0;
                this.getCropZoneByCustomId();
            },
        }
    }

</script>
<style lang="scss" scoped>
  @import "../style/theme";
  .DataLab-panelFilter {
    border-radius: 30px;
    .DataLab-panelFilter-button {
      width: 100%;
      height: 100%;
      transition: 0.3s ease-in background-color;
      position: relative;
    }
  }
</style>

<style lang="scss">
  .sumApport {
    position: absolute;
    right: 13px;
    font-size: 13px;
    z-index: 9;
    top: 65px;
    color: #3d7b60;
  }
  .myPins {
    background: #3d7b60;
    border-radius: 50%;
    margin-top: -20px;
    position: absolute;
    left: -13px;
    color: #ffffff;
    height: 30px;
    width: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 10px;
    font-weight: 800;
  }
  .multiselect_rewrite {
  cursor: pointer;
  .dropdown-toggle {
    background: #98242D !important;
    color: #fff;
    border-radius: 35px;
    border: none;
    font-weight: 700;
    .text-muted {
      color: #fff;
    }

  }
    .dropdown-menu > li > a  {
      b {
        transition: 0.2s ease-in all;
        color: #3D7B60 ;
      }
    }
  }
</style>

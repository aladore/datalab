<template>
  <section class="container">

    <data-lab-filter-species :optionSpecies="optionSpecies" :selectedSpecies="selectedSpecies" @set-specie-picked="setSpeciePicked"></data-lab-filter-species>
    <section class="row">
      <section v-if="chartOptionsRender.title && !chartOptionsRender.error" class="col-md-6">
        <div>
          <div class="card card_datalab">
            <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
              <div class="card-header-title">{{chartOptionsRender.title.label}}</div>
            </div>
            <div class="card-body">
              <highcharts style="height: 239px" class="dataLabAssolement" :options="chartOptionsRender"></highcharts>
            </div>
          </div>
        </div>

        <div v-if="chart.series[0].data[0]"  v-for="(chart, val) in chartOptionsTonne" :key="`chartTonnes-${val}`">
          <div class="card card_datalab">
            <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
              <div class="card-header-title">{{chart.title.label}}</div>
            </div>
            <div class="card-body">
<!--              <div v-if="chartOptionsPosition[val]" class="sumApport">{{chartOptionsPosition[val].sumApports.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ')}} apports</div>-->
              <highcharts style="height: 239px" class="dataLabAssolement" :options="chart" :ref="`chartTonnes-${val}`"></highcharts>
            </div>
          </div>
        </div>
      </section>
      <section class="col-md-12" v-else>
        <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
          Mon rendement n'a pas été saisie pour cette culture
        </div>
      </section>

      <section class="col-md-6">
        <div v-if="chartOptionsRenderPos.title">
          <div class="card card_datalab">
            <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
              <div class="card-header-title">{{chartOptionsRenderPos.title.label}}</div>
            </div>
            <div class="card-body">
              <div class="sumApport">{{sumApportRendement.toFixed(0).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ')}} apports</div>
              <highcharts style="height: 239px" class="dataLabAssolement" :options="chartOptionsRenderPos"></highcharts>
            </div>
          </div>
        </div>

        <div v-if="chart.series[0].data[0]"  v-for="(chart, val) in chartOptionsPosition" :key="`chartTonnes-${val}`">
          <div class="card card_datalab">
            <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
              <div class="card-header-title">{{chart.title.label}}</div>
            </div>
            <div class="card-body">
              <div class="sumApport">{{chart.sumApports.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ')}} apports</div>
              <highcharts style="height: 239px" class="dataLabAssolement" :options="chart" :ref="`chartPosition${val}`"></highcharts>
            </div>
          </div>
        </div>
        <div v-else>Pas de données pour cette espèce</div>
      </section>

    </section>
  </section>
</template>
<script>
    import { Collapse } from 'uiv';
    import {Chart} from 'highcharts-vue';
    import DataLabFilterSpecies from "../components/DataLabFilterSpecies";

    export default {
        name: "DataLabYieldDetails",
        components: {
            Collapse,
            highcharts: Chart,
            DataLabFilterSpecies
        },
        props: {
            argsHref : Array,
            userSpecies : Array,
            topSpecieId: Number,
        },
        data() {
            return {
                sumApportRendement : 0,
                skeletonChartTonne : {
                    chart: {
                        type: 'column'
                    },
                    error: false,
                    title: {
                        text: '',
                        style: {
                            color: '#3D7B60',
                            fontSize: '14px',
                            fontWeight: '300',
                            left: 0,
                            float: true,
                        },
                    },
                    colors: ['#A5D6A7', '#FFC300', '#FF5733', '#C70039', '#900C3F', '#581845'],
                    tooltip: {
                        backgroundColor: '#fff',
                        formatter: function() {
                            return '<b>' + this.series.name + '</b><br> <b>' + this.y.toFixed(1).replace('.', ',') + ' par t/hectar </b>';
                        }
                    },
                    xAxis: {
                        categories: [],
                        labels: {
                            style: {
                                color: '#3D7B60',
                                fontSize: '14px',
                                fontWeight: '600'
                            }
                        },
                    },
                    yAxis: {
                        min: 0,
                        title: {
                            align: 'high',
                            offset: 0,
                            text: 'Taux',
                            rotation: 0,
                            y: -10
                        },
                    },
                    legend: {
                        align: 'right',
                        x: -0,
                        verticalAlign: 'top',
                        y: 0,
                        floating: false,
                        shadow: false
                    },
                    plotOptions: {
                        formatter: function() {
                            return '<b>' + this.series.name + '</b><br> <b>' + this.y + ' par t/hectar </b>';
                        }
                    },
                    series: []
                },
                skeletonChartPosition: {
                    chart: {
                        type: 'column',
                    },
                    error: false,
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
                        },
                    },
                    yAxis: {
                        categories: [0],
                        min: 0,
                        title: {
                            align: 'high',
                            offset: 0,
                            text: '% bon d\'apport',
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
                        shadow: false,
                    },
                    plotOptions: {
                        series: {
                          allowPointSelect: true,
                        //   dataLabels: {
                        //       enabled: true,
                        //       borderRadius: 2,
                        //       y: -10,
                        //       shape: 'callout'
                        // },
                          marker: {
                            enabled: true,
                          },
                        }
                    },
                    series: []
                },
                moreFarmingWinter: false,
                legend: '',
                areaSpeciesFiltered: [],
                areaSpeciesUsers : [],
                translation: {
                    yield: 'Rendement',
                    tonnage: 'Rendement',
                    protein: 'Protéine',
                    humidity: 'Humidité',
                    ps: 'PS'
                },
                selectedSpecies : null,
                speciesAll: [],
                speciesTonnes : [],
                preOptionSpecies: [],
                optionSpecies: {},
                apportBy : [],
                chartOptionsTonne: {},
                chartOptionsPosition: {},
                chartOptionsRender : {},
                chartOptionsRenderPos : {},
            }
        },
        mounted() {
            this.speciesTonnes = [];
            this.speciesAll = [];
            this.preOptionSpecies = [];
            this.optionSpecies = {};
            this.apportBy = [];
            this.chartOptionsTonne = {};
            this.chartOptionsPosition = {};
            this.chartOptionsRender = {};
            this.chartOptionsRenderPos = {};
            this.getAllSpecies();
        },
        methods: {
            setSpeciePicked(specie) {
                this.speciesTonnes = [];
                this.apportBy = [];
                this.chartOptionsTonne = {};
                this.chartOptionsPosition = {};
                this.chartOptionsRender = {};
                this.chartOptionsRenderPos = {};
                this.selectedSpecies = specie;
                this.getRenderYield('user');
                this.getApportSpecie('user', 'protein');
            },
            getAllSpecies() {
                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    "params": {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCustomerSpeciesAndSap",
                        "params": {
                            "customId": suConfig.subscriber.customId,
                            "CropYear": this.argsHref[0]
                        },
                    },
                    cache: {ignoreCache: true, maxAge: 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    this.speciesAll = resp.data.result;
                    this.constructSpecieFilter();
                });
            },
            constructSpecieFilter() {
                let codeSAP = [];
                let specieSelect = [];
                this.userSpecies.forEach(specie => {
                    specieSelect.push({
                        'ids' : [specie.cropSpeciesId],
                        'season' : specie.cropSeasonLabel
                    });
                });

                this.speciesAll = this.speciesAll.sort((a, b) => (a.label > b.label) ? 1 : -1);

                this.speciesAll.forEach(specie => {
                    if(specie.codeSAP) {
                        codeSAP.push(specie.codeSAP);
                    }
                    let findObjById = specieSelect.find(obj => {
                        return obj.ids.includes(specie.id);
                    });
                    if(findObjById && findObjById.season) {
                        this.preOptionSpecies.push({
                            codeSAP: specie.codeSAP,
                            id: specie.codeSAP ? specie.codeSAP : specie.id,
                            oldId: specie.id,
                            label: specie.label,
                            season: findObjById.season,
                        });
                    }
                });

                let options = this.preOptionSpecies.sort((a, b) => (a.season < b.season) ? 1 : -1);;

                const arrayHashmap = options.reduce((obj, item) => {
                    if(obj[item.season]) {
                        obj[item.season].push(item);
                    } else{
                        item.season = item.season;
                        (obj[item.season] = [{ ...item }]);
                    }
                    return obj;
                }, {});
                this.optionSpecies = arrayHashmap;

                let indexedOption = Object.values(arrayHashmap);

                // if(this.topSpecieId) {
                  let optionFlat = indexedOption.flat();

                    if(this.topSpecieId) {
                        let specie1 = optionFlat.filter(specie => specie.oldId == this.topSpecieId);

                        if(specie1.length) {
                            this.selectedSpecies = {
                                'oldId' : specie1[0].oldId,
                                'id' :  specie1[0].codeSAP ? specie1[0].codeSAP : specie1[0].oldId,
                                'label' : specie1[0].label,
                            }
                        } else {
                            this.selectedSpecies = {
                                'oldId' : indexedOption[0][0].oldId,
                                'id' : indexedOption[0][0].codeSAP,
                                'label' : indexedOption[0][0].label,
                            }
                        }
                    } else {
                        this.selectedSpecies = {
                            'oldId' : indexedOption[0][0].oldId,
                            'id' : indexedOption[0][0].codeSAP,
                            'label' : indexedOption[0][0].label,
                        }
                    }
                // }

                this.getRenderYield('user');
                this.getApportSpecie('user', 'protein');
            },
            getApportSpecie(by, element) {
                let params = {};
                params.CropYear = parseInt(this.argsHref[0]);
                params.CodeSAP = [this.selectedSpecies.id];
                params.Type = element;

                if(by == 'user') {
                    params.customId = suConfig.subscriber.customId;
                } else {
                    params.Latitude = parseFloat(this.argsHref[2]);
                    params.Longitude = parseFloat(this.argsHref[3]);
                    params.Distance = parseInt(this.argsHref[1]);
                }

                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    "params": {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabApportEntity",
                        "method": "findByTypeStats",
                        params
                    },
                    cache: {ignoreCache: true, maxAge: 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    this.apportBy[by] = resp.data;
                    if(by == 'user') {
                        this.getApportSpecie('area', element);
                    } else {
                        if(element == 'protein') {
                            this.generateChartTonnes(by, element, this.apportBy);
                        }
                        if(element == 'humidity') {
                            this.generateChartTonnes(by, element, this.apportBy);
                        }
                        if(element == 'ps') {
                            this.generateChartTonnes(by, element, this.apportBy);
                        }
                    }
                });
            },
            generateChartTonnes(by, element, array) {
                let dataUser = 0, dataArea = 0;
                let label = this.translation[element];
                const chart = JSON.parse(JSON.stringify(this.skeletonChartTonne));
                let compareGram = 'comparée';
                let prefix = '%';
                if(element == 'ps') {
                    compareGram = 'comparé';
                    prefix = '';
                }
                chart.title.label =  this.translation[element] + ' '+ compareGram +' au territoire - ' + this.selectedSpecies.label +' - '+ this.argsHref[0];;

                let elementCapitalize = element.charAt(0).toUpperCase() + element.slice(1);
                if(array.user[0]) {
                    dataUser = parseFloat(parseFloat(array.user[0][elementCapitalize]).toFixed(1))
                }
                if(array.area[0]) {
                    dataArea = parseFloat(parseFloat(array.area[0][elementCapitalize]).toFixed(1))
                }

                chart.series = [
                    {
                        name: "Mon exploitation",
                        data: [dataUser],
                        tooltip: {
                            backgroundColor: '#fff',
                            headerFormat: '',
                            enabled : true,
                            pointFormatter: function (el) {
                                return '<div>'+ label +' : <strong>' + this.y.toFixed(1).replace('.', ',') + prefix + '</strong></div>'
                            }
                        }
                    },
                    {
                        name: "Moyenne territoire",
                        data: [dataArea],
                        tooltip: {
                            backgroundColor: '#fff',
                            headerFormat: '',
                            enabled : true,
                            pointFormatter: function (el) {
                                return '<div>'+ label +' : <strong>' + this.y.toFixed(1).replace('.', ',') +  prefix +'</strong></div>'
                            }
                        }
                    }
                ];
                chart.xAxis.categories.push(this.translation[element.toLowerCase()]);
                chart.yAxis.title.text = '% '+ this.translation[element.toLowerCase()];


                if(element == 'protein') {
                    dataUser ? this.getApportElement(element, dataUser) : true;
                    this.getApportSpecie('user','humidity');
                }
                if(element == 'humidity') {
                    dataUser ? this.getApportElement(element, dataUser) : true;
                    this.getApportSpecie('user','ps');
                }
                if(element == 'ps') {
                    dataUser ? this.getApportElement(element, dataUser) : true;
                }

                this.$set(this.chartOptionsTonne, element, chart);
            },
            getApportElement(element, dataUser) {
                let params = {};
                params.CropYear = parseInt(this.argsHref[0]);
                params.CodeSAP = [this.selectedSpecies.id];
                params.Type = element;
                params.Latitude = parseFloat(this.argsHref[2]);
                params.Longitude = parseFloat(this.argsHref[3]);
                params.Distance = parseInt(this.argsHref[1]);
                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    "params": {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabApportEntity",
                        "method": "findByStepPosition",
                        params
                    },
                    debug: true,
                    cache: {ignoreCache: true, maxAge: 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    let dataAreaSorted = resp.data.sort((a, b) => (parseFloat(a.Step) > parseFloat(b.Step)) ? 1 : -1);

                    // Set ratio for position
                    if(dataAreaSorted.length > 100) {
                        let range = 0.5;
                        let lastData = dataAreaSorted[0];
                        let notInRange = [];
                        let lastDataRef = dataAreaSorted[0];
                        let dataRanged = [];
                        dataAreaSorted.forEach(data => {
                            if(parseFloat(data.Step) - lastDataRef.Step < range && lastDataRef.Step != parseFloat(data.Step)) {
                                if(!notInRange.length) {
                                    lastDataRef = lastData;
                                }
                                notInRange.push(data);
                            } else {
                                if(notInRange.length) {
                                    let newRange = {}
                                    let sumStep = 0;
                                    notInRange.forEach(data => {
                                        newRange.Apport =  parseFloat(lastDataRef.Apport) + parseFloat(data.Apport);
                                        newRange.percentage = parseFloat(lastDataRef.percentage) + parseFloat(data.percentage);
                                        sumStep += parseFloat(data.Step)
                                    });
                                    let avgStep = ((parseFloat(lastDataRef.Step) + sumStep) / (notInRange.length+1)).toFixed(1);
                                    newRange.Step = avgStep;
                                    dataRanged.push(newRange);
                                } else {
                                    dataRanged.push(lastDataRef);
                                }
                                lastDataRef = data;
                                notInRange = [];
                            }
                            lastData = data;
                        });
                        dataAreaSorted = [...dataRanged];
                    }

                    let sumApport = 0;
                    let maxPercentage = 0;
                    dataAreaSorted.forEach(apport => {
                        sumApport += parseInt(apport.Apport);
                    });
                    dataAreaSorted.map(apport=> {
                        apport.percentage = parseInt(apport.Apport) / sumApport * 100;
                        if(apport.percentage > maxPercentage) {
                            maxPercentage = apport.percentage;
                        }
                    });
                    this.generateChartPosition(dataAreaSorted, dataUser, element, maxPercentage, sumApport);
                });
            },
            generateChartPosition(areaData, dataUser, element, maxPercentage, sumApport) {
                let label = this.translation[element];
                const chart = JSON.parse(JSON.stringify(this.skeletonChartPosition));
                let prefix = '%';
                let compareGram = 'comparée';
                if(element == 'ps') {
                    compareGram = 'comparé';
                    prefix = '';
                }
                chart.title.label =  this.translation[element] + ' '+ compareGram +' au territoire - ' + this.selectedSpecies.label +' - '+ this.argsHref[0];;
                chart.sumApports = sumApport;
                chart.series = [
                    {
                        name: "Mon territoire",
                        data: [],
                        color: 'rgb(255, 195, 0)',
                        pointWidth: 5,
                        tooltip: {
                            backgroundColor: '#fff',
                            pointFormatter: function(el) {
                                return '<div>'+ label +': <strong>'+this.category.toString().replace('.', ',')+prefix+'</strong></div>' +
                                    '<div> Bon d\'apport : <strong>'+this.y.toFixed(1).replace('.', ',') +prefix+'</strong></div>'
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
                            enabled : false,
                            pointFormatter: function (el) {
                                return '<div>'+ label +' : <strong>' + this.ift.toFixed(1).replace('.', ',') +prefix+ '</strong></div>'
                                    // + '<div> % de parcelles : <strong>' + this.cropCount + '</strong></div>'
                            }
                        }
                    }
                ];

                let lastAreaYield = 0;
                let indexUser = 0;
                areaData.forEach((data, index) => {
                    chart.xAxis.categories.push(parseFloat(data.Step));
                    chart.series[0].data.push(parseFloat(data.percentage));

                    if((dataUser >= lastAreaYield && dataUser < parseFloat(data.Step))) {
                        indexUser = index;
                        chart.series[1].data.push(
                            {
                              y: maxPercentage,
                              ift: parseFloat(dataUser),
                            }
                        );
                    } else {
                        if(index != 0) {
                            chart.series[1].data.push('');
                        }
                    }

                    lastAreaYield = parseFloat(data.Step)
                });
                this.$set(this.chartOptionsPosition, element, chart);
            },
            getRenderYield(by) {
                let params = {};
                params.CropYear = parseInt(this.argsHref[0]);
                params.SpeciesId = this.selectedSpecies.oldID;
                params.CorrectYield = true;
                if(by == 'user') {
                    params.customId = suConfig.subscriber.customId;
                } else {
                    params.Latitude = parseFloat(this.argsHref[2]);
                    params.Longitude = parseFloat(this.argsHref[3]);
                    params.Distance = parseInt(this.argsHref[1]);
                }
                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    "params": {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCropZoneYield",
                        params
                    },
                    cache: {ignoreCache: true, maxAge: 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    let findObjSpecie = resp.data.result.find(obj => {
                        return obj.CropSpeciesId == this.selectedSpecies.oldId;
                    });

                    if(!findObjSpecie) {
                        return this.chartOptionsRender.error = true
                    }
                    this.speciesTonnes[by] = findObjSpecie;
                    if(by == 'user') {
                        this.getRenderYield('area');
                    }
                    if(by == 'area') {
                        this.chartOptionsRender = JSON.parse(JSON.stringify(this.skeletonChartTonne));
                        this.chartOptionsRender.title.label = 'Rendement t/ha comparé au territoire - ' + this.selectedSpecies.label +' - ' + this.argsHref[0];
                        let dataUser = parseFloat(parseFloat(this.speciesTonnes.user.YieldAvg).toFixed(1)),
                            dataArea = parseFloat(parseFloat(this.speciesTonnes.area.YieldAvg).toFixed(1))
                        this.chartOptionsRender.series = [
                            {
                                name: "Mon exploitation",
                                data: [dataUser],
                                tooltip: {
                                    backgroundColor: '#fff',
                                    headerFormat: '',
                                    enabled : false,
                                    pointFormatter: function (el) {
                                        return '<div> Mon rendement : <strong>' + this.y.toFixed(1).replace('.', ',') + ' t/ha</strong></div>'
                                    }
                                }
                            },
                            {
                                name: "Moyenne territoire",
                                data: [dataArea],
                                tooltip: {
                                    backgroundColor: '#fff',
                                    headerFormat: '',
                                    enabled : false,
                                    pointFormatter: function (el) {
                                        return '<div> Moyenne territoire : <strong>' + this.y.toFixed(1).replace('.', ',') + ' t/ha</strong></div>'
                                    }
                                }
                            }
                        ];
                        this.chartOptionsRender.yAxis.title.text = 't/ha';
                        this.chartOptionsRender.xAxis.categories.push('Rendement');
                        this.getRenderYieldPos(dataUser)
                    }
                });
            },
            getRenderYieldPos(dataUser) {
                let params = {};
                params.CropYear = parseInt(this.argsHref[0]);
                params.SpeciesId = [this.selectedSpecies.oldId];
                params.Latitude = parseFloat(this.argsHref[2]);
                params.Longitude = parseFloat(this.argsHref[3]);
                params.Distance = parseInt(this.argsHref[1]);
                params.CorrectYield = true;
                params.debug = true;

                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    "params": {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCropZoneYieldRange",
                        params
                    },
                    debug : true,
                    cache: {ignoreCache: true, maxAge: 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    resp.data.result.forEach(res => {
                        this.sumApportRendement += parseInt(res.Count);
                    });

                    let dataAreaSorted = resp.data.result.sort((a, b) => (parseFloat(a.Yield) > parseFloat(b.Yield)) ? 1 : -1);

                    let sumApport = 0;
                    let maxPercentage = 0;

                    dataAreaSorted.forEach(apport => {
                        sumApport += parseInt(apport.Count);
                    });

                    dataAreaSorted.map(apport=> {
                        apport.percentage = parseInt(apport.Count) / sumApport * 100;
                        if(apport.percentage > maxPercentage) {
                            maxPercentage = apport.percentage;
                        }
                    });

                    this.chartOptionsRenderPos = {};
                    let label = 'Rendement' ;
                    this.chartOptionsRenderPos = JSON.parse(JSON.stringify(this.skeletonChartPosition));
                    this.chartOptionsRenderPos.title.label = 'Mon rendement comparé au territoire - '+ this.selectedSpecies.label +' - '+ this.argsHref[0];
                    this.chartOptionsRenderPos.series = [
                        {
                            name: "Mon territoire",
                            data: [],
                            color: 'rgb(255, 195, 0)',
                            pointWidth: 5,
                            tooltip: {
                                backgroundColor: '#fff',
                                pointFormatter: function(el) {
                                    return '<div>'+ label +': <strong>'+this.category.toString().replace('.', ',')+' t/ha</strong></div>' +
                                        '<div> % de parcelles : <strong>'+this.y.toFixed(1).replace('.', ',') +'%</strong></div>'
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
                                    return '<div>'+ label +' : <strong>' + this.ift.toFixed(1).replace('.', ',') + '</strong> t/ha</div>'
                                }
                            }
                        }
                    ];
                    let lastAreaYield = 0;

                    dataAreaSorted.forEach((data, index) => {
                        this.chartOptionsRenderPos.xAxis.categories.push(parseFloat(data.Yield));
                        this.chartOptionsRenderPos.series[0].data.push(parseFloat(data.percentage));

                        if((dataUser >= lastAreaYield && dataUser < parseFloat(data.Yield))) {
                            this.chartOptionsRenderPos.series[1].data.push(
                                {
                                    y: maxPercentage,
                                    ift: parseFloat(dataUser),
                                    marker : {
                                        symbol: 'url(https://www.hightcharts.com/samples/graphics/sun.png)'
                                    }}
                            );
                        } else {
                            if(index != 0) {
                                this.chartOptionsRenderPos.series[1].data.push('');
                            }
                        }
                        lastAreaYield = parseFloat(data.Yield);
                    });
                    this.chartOptionsRenderPos.yAxis.title.text = '% de parcelles';
                });
            },
            legendClick(legend) {
                const index = legend.target.index;
                const isVisible = legend.target.visible;

                this.$refs.chart.chart.series[0].data.forEach(point => {
                    if (point.index === index) {
                        point.update({
                            visible: !isVisible
                        });
                    }
                });
            },
        },
        watch: {
            argsHref: function (newFarm, oldFarm) {
                this.speciesTonnes = [];
                this.apportBy = [];
                this.chartOptionsTonne = {};
                this.chartOptionsPosition = {};
                this.chartOptionsRender = {};
                this.chartOptionsRenderPos = {};
                this.getRenderYield('user');
                this.getApportSpecie('user', 'protein');
            }
        },
    }


</script>
<style lang="scss" scoped>
  @import "../style/theme";
  .sumApport {
    position: absolute;
    right: 13px;
    font-size: 13px;
    z-index: 9;
    top: 40px;
    color: #3d7b60;
  }
</style>
<style>
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
</style>

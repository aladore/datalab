<template>
  <section class="col-md-6">
    <div class="card card_datalab">
      <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
        <div class="card-header-title">Rendements des cultures principales (t/ha) <span> - {{this.argsHref[0]}}</span></div>
        <a class="bg-secondary card-header-button" :key="links.yield.route" :href="links.yield.route">En savoir +</a>
      </div>
      <div class="card-body">
        <highcharts style="height: 239px" class="dataLabAssolement" :options="chartOptions" ref="chart"></highcharts>
      </div>
    </div>
  </section>
</template>
<script>

    import {Chart} from 'highcharts-vue'

    export default {
        name: "DataLabYield",
        components: {
            highcharts: Chart
        },
        props : {
            top3UserFarming : Array,
            top3AreaFarming : Array,
            top3Farming : Object,
            argsHref : Array,
        },
        data() {
            return {
                messageError: '',
                links: {
                    'yield' : {route: '#yield/'+ this.argsHref[0]+'/'+this.argsHref[1]+'/'+this.argsHref[2]+'/'+this.argsHref[3], name: "DataLabYieldDetails"}
                },
                chartOptions: {
                    chart: {
                        type: 'column'
                    },
                    colors: ['#A5D6A7', '#FFC300', '#FF5733', '#C70039', '#900C3F', '#581845'],
                    title: {
                        text: ''
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
                            text: 't/ha',
                            rotation: 0,
                            y: -10
                        },
                    },
                    tooltip: {
                        backgroundColor: '#fff',
                        shared: true,
                        valueDecimals: 2,
                        valueSuffix: ' t/ha'
                        // formatter: function() {
                        //     return '<b>' + this.series.name + '</b><br> Rendement: <b>' + this.y.toFixed(1).replace('.', ',') + '</b> t/ha  ';
                        // }
                    },
                    accessibility: {
                        announceNewData: {
                            enabled: true
                        }
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

                    },
                    series: []
                },
                speciesBySAP: [],
                top3SAP: [],
                yield : [],
                yieldResult : [],
                speciesId : [],
            }
        },
        mounted() {
            this.chartOptions.series = [];
            this.chartOptions.xAxis.categories = [];
            this.getYield('user');
        },
        methods : {
            getYield(by) {
                let params = {};
                params.CropYear = parseInt(this.argsHref[0]);
                if(by == 'user') {
                    params.customId = suConfig.subscriber.customId;
                } else {
                    params.Latitude = parseFloat(this.argsHref[2]);
                    params.Longitude = parseFloat(this.argsHref[3]);
                    params.Distance = parseInt(this.argsHref[1]);
                }

                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    params : {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCropZoneArea",
                        params,
                    },
                    cache: {ignoreCache: true}
                }).then((resp) => {
                    let totalHectar = 0;
                    resp.data.result.forEach(data => totalHectar += parseFloat(data.Sum));
                    let speciesRank = resp.data.result.sort((a, b) => (parseFloat(a.Sum) < parseFloat(b.Sum)) ? 1 : -1);

                    this.yield[by] = speciesRank.slice(0, 3);
                    this.yield[by].hectars = totalHectar;

                    if(by == 'user') {
                        this.getYield('area');
                    }
                    if(by == 'area') {
                        // this.getYield('area');
                        this.getApportSpecie('user');
                    }
                });
            },
            getApportSpecie(by) {
                let params = {};
                params.CropYear = parseInt(this.argsHref[0]);
                params.SpeciesId = [this.yield.user[0].cropSpeciesId, this.yield.user[1].cropSpeciesId, this.yield.user[2].cropSpeciesId];
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
                        params,
                        "debug" :true
                    },
                    cache: {ignoreCache: true, maxAge: 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    this.yieldResult[by] = resp.data.result;
                    this.generateChart(by);
                })
            },
          generateChart(by) {
              if(by == 'user') {
                  this.chartOptions.series.push({
                      name: "Mon exploitation",
                      data: []
                  });

                  this.yieldResult[by].forEach(specie => {
                      this.chartOptions.series[0].data.push(parseFloat(specie.YieldAvg))
                      this.chartOptions.xAxis.categories.push(specie.CropSpeciesLabel);
                  });
                  this.getApportSpecie('area');
              }

              if(by == 'area') {
                  this.chartOptions.series.push({
                      name: "Moyenne territoire",
                      data: []
                  });
                  this.yieldResult[by].forEach((specie, index) => {
                      this.chartOptions.series[1].data.push(parseFloat(specie.YieldAvg));
                      if(!this.chartOptions.series[0].data[index]) {
                          this.chartOptions.xAxis.categories.push(specie.CropSpeciesLabel);
                      }
                  });
              }
          }
        },
        watch: {
              argsHref: function (newFarm, oldFarm) {
                  this.yield = {route: '#yield/'+ this.argsHref[0]+'/'+this.argsHref[1]+'/'+this.argsHref[2]+'/'+this.argsHref[3], name: "DataLabYieldDetails"}

                  this.chartOptions.series = [];
                  this.chartOptions.xAxis.categories = [];
                  this.speciesBySAP = [];
                  this.top3SAP = [];
                  this.yield = [];
                  this.yieldResult = [];
                  this.speciesId = [];
                  this.getYield('user');
            },
        },
    }


</script>
<style lang="scss" scoped>
  @import "../style/theme";
</style>

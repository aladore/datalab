<template>
  <section class="col-md-6">
    <div class="card card_datalab">
      <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
        <div class="card-header-title">Part des 3 principales cultures dans l’assolement <span> - {{this.argsHref[0]}}</span></div>
        <a class="bg-secondary card-header-button" :key="links.assolement.route" :href="links.assolement.route" >En savoir +</a>
      </div>
      <div class="card-body">
        <highcharts v-if="!messageError" style="height: 239px" class="dataLabAssolement" :options="chartOptions" ref="chart"></highcharts>
        <div v-else>{{messageError}}</div>
      </div>
    </div>
  </section>
</template>
<script>

    import {Chart} from 'highcharts-vue';

    export default {
        name: "DataLabAssolement",
        components: {
            highcharts: Chart
        },
        props: {
            top3UserFarming : Array,
            top3AreaFarming : Array,
            top3Farming : Object,
            argsHref : Array
        },
        data() {
            return {
                chartOptions: {
                    chart: {
                        type: 'column'
                    },
                    colors: ['#A5D6A7', '#FFC300', '#FF5733', '#C70039', '#900C3F', '#581845'],
                    title: {
                        text: ''
                    },
                    xAxis: {
                        categories: ['Mon Exploitation', 'Territoire'],
                        labels: {
                            style: {
                                color: '#3D7B60',
                                fontSize: '14px',
                                fontWeight: '600'
                            }
                        },
                    },
                    yAxis: {
                        // min: 0,
                        // max: 100,
                        title: {
                            align: 'high',
                            offset: 0,
                            text: '%',
                            rotation: 0,
                            y: -20
                        },
                    },
                    tooltip: {
                        backgroundColor: '#fff',
                        formatter: function() {
                            let currentBar = this.point.index;
                            return'<b>' + this.series.name + '</b><br>' + '<b>' + this.y.toFixed(1).replace('.', ',') + ' % </b><br><b>' +
                                parseFloat(this.series.options.hectars[currentBar]).toFixed(1).replace('.', ',') + ' ha </b>';
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
                        series: {
                            stacking: 'normal'
                        }
                    },
                    series: []
                },
                messageError : '',
                links: {
                    'assolement' : {route: '#assolement/'+ this.argsHref[0]+'/'+this.argsHref[1]+'/'+this.argsHref[2]+'/'+this.argsHref[3], name: "DataLabAssolementDetails"}
                },
                assolements: {},
            }
        },
        mounted() {
            this.chartOptions.series = [];
            this.chartOptions.xAxis.categories = [];
            this.getAssolements('user');
        },
        methods : {
            getAssolements(by) {
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
                    let rankingAssolement = resp.data.result.sort((a, b) => (parseFloat(a.Sum) < parseFloat(b.Sum)) ? 1 : -1);
                    this.assolements[by] = rankingAssolement;
                    this.assolements[by].hectars = totalHectar;

                    this.generateChart(by);
                });
            },
            generateChart(by) {
                let top3 = this.assolements[by].slice(0, 3);
                //User data
                if(by == 'user') {
                    this.chartOptions.xAxis.categories.push('Mon Exploitation');
                    top3.forEach((specie, index) => {
                        let hectars = parseFloat(specie.Sum);
                        let specieHectar = parseFloat(hectars);
                        let AVG = hectars/this.assolements[by].hectars*100;
                        let newLine = {name: specie.Label, id: specie.Label , hectars: [parseFloat(specieHectar.toFixed(1).replace('.', ','))], data: [AVG]}
                        // this.chartOptions.series.push(newLine);
                        this.$set(this.chartOptions.series, index, newLine)
                    });

                    this.getAssolements('area');
                }

                //Area data
                if(by == 'area') {
                    this.chartOptions.xAxis.categories.push('Mon Territoire');
                    top3.forEach((specie, index) => {
                        let hectars = parseFloat(specie.Sum);
                        let specieHectar = parseFloat(hectars);
                        let AVG = hectars/this.assolements[by].hectars*100;

                        let findDataBar = this.chartOptions.series.find(obj => {
                            return obj.name == specie.Label;
                        });

                        if(findDataBar) {
                            findDataBar.hectars.push(specieHectar);
                            findDataBar.data.push(AVG);
                        } else {
                            let newLine = {name: specie.Label, id: specie.Label , hectars: [0,specieHectar], data: [0,AVG]};
                            this.chartOptions.series.push(newLine);
                        }
                    });
                }
            }
        },
        watch: {
            argsHref: function (newFarm, oldFarm) {
                this.links.assolement = {route: '#assolement/'+ this.argsHref[0]+'/'+this.argsHref[1]+'/'+this.argsHref[2]+'/'+this.argsHref[3], name: "DataLabAssolementDetails"};
                this.chartOptions.series = [];
                this.chartOptions.xAxis.categories = [];
                this.assolements = {};
                this.getAssolements('user');

            },
        },
    }


</script>
<style lang="scss" scoped>
  @import "../style/theme";
</style>

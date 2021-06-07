<template>
  <section class="container">

    <div class="modalPersonal" style="position: fixed; z-index: 999;width: 100%;left: 10%;width: 80%; top:10%; bottom: 10%" v-if="modalOther"  id="modalOther" tabindex="-1" role="dialog" aria-labelledby="modalOther" aria-hidden="true">
      <div class="modal-body" style="width: 100%;">
          <div  @click="modalOther = !modalOther" type="button" class="close" data-dismiss="modal" aria-label="Close">
            <i class="fa fa-close" aria-hidden="true"></i>
          </div>
          <div class="titleModalOther">Autres cultures</div>
          <div style="text-align: center; padding: 8px;">
            <button @click="onVisibleOther" class="btn btn-primary">
              <span v-if="visibleOther">Tout désélectionner</span>
              <span v-else>Tout sélectionner</span>
            </button>
          </div>
          <highcharts style="height: 700px;" class="dataLabAssolement" :options="chartAssolementsOther" ref="chartOther"></highcharts>
    </div>
    </div>

    <div class="row">
      <div class="col-md-12 mt-5">
        <div class="card card_datalab">
          <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
            <div class="card-header-title">Mon assolement comparé au territoire - {{argsHref[0]}} </div>
          </div>
          <div class="card-body">
            <div class="text-primary text-center text-lg p-5">Cultures principales</div>
            <div class="col-xl-12 col-md-12">
              <highcharts style="height: 500px;" class="dataLabAssolement" :options="chartAssolements" ref="chart"></highcharts>
            </div>
            <div>
              <span style="position: relative; left: 25%; z-index: 99; color: #3D7B60; font-size: 15px; font-weight: 600">{{ totalHaUser.toFixed(1)}} ha</span>
              <span style="position: relative; left: 70%; z-index: 99; color: #3D7B60; font-size: 15px; font-weight: 600">{{totalHaArea.toFixed(1)}} ha</span>
            </div>
            <!--LEGEND-->
            <div class="col-xl-12 col-md-12" >
              <highcharts style="height: 100px;" class="dataLabAssolement1" :options="chartLegend" ref="chart1"></highcharts>
            </div>
          </div>
          <div style=" margin-top: 1rem;margin-bottom: 1rem;border: 0;border-top: 1px solid rgba(0, 0, 0, 0.1);"></div>
        </div>
      </div>
    </div>
  </section>
</template>
<script>
    import {Chart} from 'highcharts-vue'
    import DataLabFilterSpecies from "../components/DataLabFilterSpecies";

    export default {
        name: "DataLabAssolementDetails",
        components: {
            highcharts: Chart,
            DataLabFilterSpecies
        },
        props: {
            farming : Object,
            argsHref : Array,
        },
        data() {
            return {
                visibleOther: true,
                modalOther: false,
                userSpecies: null,
                show: false,
                legend: '',
                chartAssolements: {
                    chart: {
                        type: 'pie',
                        events: {
                            load(e) {
                                // console.log(e.target)
                                e.target.tooltips = e.target.series.slice(1).map(series => {
                                    new Highcharts.Tooltip(e.target, Highcharts.merge(e.target.options.tooltip))
                                });
                            },
                        },
                        plotBackgroundColor: null,
                        plotBorderWidth: null,
                        plotShadow: false,
                    },
                    colors: [
                        '#00876c',
                        '#4e9874',
                        '#78a97f',
                        '#9dba8f',
                        '#bfcba4',
                        '#ddddbc',
                        '#f8f1d8',
                        '#efd8b3',
                        '#e9be91',
                        '#e5a276',
                        '#e18461',
                        '#dc6455',
                        '#d43d51'
                    ],
                    title: {
                        text: ''
                    },
                    labels: {
                        items: [{
                            html: 'Mon exploitation',
                            style: {
                                left: '240px',
                                top: '0px',
                                color: '#3D7B60',
                                fontSize: '20px'
                            }
                        },
                        {
                            html: 'Mon territoire',
                            style: {
                                left: '840px',
                                top: '0px',
                                color: '#3D7B60',
                                fontSize: '20px'
                            }
                        }]
                    },
                    xAxis: {
                        categories: ['Mon exploitation', 'Territoire'],
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
                            text: ''
                        }
                    },
                    legend: {
                        useHTML: true,
                        enabled: false,
                        align: 'right',
                        x: 0,
                        y: 0,
                        verticalAlign: 'top',
                        horizontalAlign: 'right',
                        layout: 'vertical',
                        floating: true,
                        shadow: false,
                        useHTML: true,
                        symbolWidth: 0,
                        // labelFormatter: function () {
                        //     let categorie = '';
                        //     if (this.categorie != undefined) {
                        //         categorie = '<div style="color: #3D7B60; font-size:15px ">' + this.categorie + '</div>'
                        //     }
                        //     return '<div style="position:relative;">' +
                        //         '<div style="position: absolute; top:-15px">' + categorie + '</div>' +
                        //         '<div>' +
                        //         '<span class="ml-2 p-2"> ' + this.name + ' </span>' +
                        //         '</div>' +
                        //         '</div>'
                        // }
                    },
                    accessibility: {
                        point: {
                            valueSuffix: '%'
                        }
                    },
                    plotOptions: {
                        series: {
                            point: {
                                events: {
                                    legendItemClick: (event) => {
                                        this.legendClick(event);
                                    },
                                    mouseOver: (event) => {
                                        this.specieHover(event)
                                    },
                                    click: (event) => {
                                        this.partClicked(event)
                                    }
                                }
                            },
                            states: {
                                hover: {
                                    enabled: true
                                }
                            }
                        }
                    },
                    series: [{
                        id: 'Exploitation',
                        name: 'Mon exploitation',
                        size: '400',
                        center: [300, 230],
                        dataLabels: {
                            enabled: true,
                            format: '<b>{point.name}</b>: {point.percentage:.1f} %'
                        },
                        data: [],
                        tooltip: {
                            useHTML: true,
                            backgroundColor: '#fff',
                            enabled : true,
                            formatter: function() {
                                return'<b style="padding: 8px">' + this.key + '</b><br>' + '<b>' + this.y.toFixed(1).replace('.', ',') + ' % </b><br><b>'+ this.point.hectar +' ha</b>';
                            }
                        },
                    },
                    {
                        dataLabels: {
                            enabled: false,
                            format: '<b>{point.name}</b>: {point.percentage:.1f} %'
                        },
                        id: 'Territoire',
                        name: 'Territoire',
                        size: '400',
                        center: [900, 230],
                        data: [],
                        tooltip: {
                            backgroundColor: '#fff',
                            enabled : true,
                            useHTML: true,
                            formatter: function() {
                                return'<b>' + this.key + '</b><br>' + '<b>' + this.y.toFixed(1).replace('.', ',') + ' % </b><br><b>'+ this.point.hectar +' ha</b>';
                            }
                        },
                    }],
                    tooltip: {
                        backgroundColor: '#fff',
                        useHTML: true,
                        formatter: function() {
                            let comparison = '';
                            let details = '';
                            let message = '';
                            if(this.key == 'Autres') {
                                message = '(Affichage des cultures inférieur à 3%)'
                                details = '<i style="display:block;margin-top: 8px;">cliquer pour afficher le détail</i>';
                            }
                            if(this.series.compare) {
                                if (this.series.compare.key == this.key) {
                                    comparison += '<b style="display:block;margin-bottom: 3px;margin-top: 8px;">'+this.series.compare.serieName +'<br></b>'
                                        + this.series.compare.y.toFixed(1).replace('.', ',') + ' % <br>'+ this.series.compare.hectar +' ha<br>';
                                }
                                return'<div style="margin-bottom: 3px;display:block;"><strong style="font-size: 18px;">'+this.key+ '</strong> ' + message + '<br></div>' +
                                    '<b style="margin-bottom: 3px;display:block;">'+this.series.name +'<br></b>' +
                                    this.y.toFixed(1).replace('.', ',') + ' % <br>'+
                                    this.point.hectar +' ha<br>' +
                                    comparison
                                    + details
                            }
                           }
                    },
                },
                chartLegend: {
                    chart: {
                        type: 'pie',
                        plotBackgroundColor: null,
                        plotBorderWidth: null,
                        plotShadow: false,
                    },
                    colors: [
                        '#00876c',
                        '#4e9874',
                        '#78a97f',
                        '#9dba8f',
                        '#bfcba4',
                        '#ddddbc',
                        '#f8f1d8',
                        '#efd8b3',
                        '#e9be91',
                        '#e5a276',
                        '#e18461',
                        '#dc6455',
                        '#d43d51'],
                    title: {
                        text: ''
                    },
                    labels: {
                        items: [
                            {
                                // html: 'Mon territoire',
                                style: {
                                    left: '250px',
                                    top: '230px',
                                    color: '#3D7B60',
                                    fontSize: '15px'
                                }
                            }
                        ]
                    },
                    xAxis: {
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
                            text: ''
                        }
                    },
                    legend: {
                        useHTML: true,
                        enabled: true,
                        align: 'right',
                        x: 0,
                        y: 0,
                        verticalAlign: 'top',
                        horizontalAlign: 'right',
                        layout: 'horizontal',
                        floating: true,
                        shadow: false,
                        useHTML: true,
                        symbolWidth: 0,
                        labelFormatter: function () {
                            let categorie = '';
                            if (this.categorie != undefined) {
                                categorie = '<div style="color: #3D7B60; font-size:15px ">' + this.categorie + '</div>'
                            }
                            return '<div style="position:relative;">' +
                                '<div style="position: absolute; top:-15px">' + categorie + '</div>' +
                                '<div>' +
                                '<span class="ml-2 p-2"> ' + this.name + ' </span>' +
                                '</div>' +
                                '</div>'
                        }
                    },
                    accessibility: {
                        point: {
                            valueSuffix: '%'
                        }
                    },
                    plotOptions: {
                        pie: {
                            allowPointSelect: true,
                            cursor: 'pointer',
                            dataLabels: {
                                enabled: false
                            },
                            states: {
                                // hover: {
                                //     brightness: 0.5
                                // }
                            },
                            showInLegend: true,
                            point: {
                                events: {
                                    legendItemClick: (event) => {
                                        this.legendClick(event);
                                    },
                                }
                            }
                        },
                    },
                    series: [
                        {
                            id: 'Territoire',
                            name: 'Territoire',
                            size: '0',
                            // center: [500, 100],
                            center: [250, 150],
                            data: []
                        }],
                    tooltip: {
                        backgroundColor: '#fff',
                        formatter: function() {
                            return'<b>' + this.key + '</b><br>' + '<b>' + this.y.toFixed(1).replace('.', ',') + ' % </b><br><b>'+ this.point.hectar +' ha</b>';
                        }
                    },
                },
                chartAssolementsOther : {
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
                        min: 0,
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
                chartLegendOther: {
                    chart: {
                        type: 'pie',
                        plotBackgroundColor: null,
                        plotBorderWidth: null,
                        plotShadow: false,
                    },
                    colors: [
                        '#00876c',
                        '#4e9874',
                        '#78a97f',
                        '#9dba8f',
                        '#bfcba4',
                        '#ddddbc',
                        '#f8f1d8',
                        '#efd8b3',
                        '#e9be91',
                        '#e5a276',
                        '#e18461',
                        '#dc6455',
                        '#d43d51'],
                    title: {
                        text: ''
                    },
                    labels: {
                        items: [
                            {
                                // html: 'Mon territoire',
                                style: {
                                    left: '250px',
                                    top: '230px',
                                    color: '#3D7B60',
                                    fontSize: '15px'
                                }
                            }
                        ]
                    },
                    xAxis: {
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
                            text: ''
                        }
                    },
                    legend: {
                        useHTML: true,
                        enabled: true,
                        align: 'right',
                        x: 0,
                        y: 0,
                        verticalAlign: 'top',
                        horizontalAlign: 'right',
                        layout: 'horizontal',
                        floating: true,
                        shadow: false,
                        useHTML: true,
                        symbolWidth: 0,
                        labelFormatter: function () {
                            let categorie = '';
                            if (this.categorie != undefined) {
                                categorie = '<div style="color: #3D7B60; font-size:15px ">' + this.categorie + '</div>'
                            }
                            return '<div style="position:relative;">' +
                                '<div style="position: absolute; top:-15px">' + categorie + '</div>' +
                                '<div>' +
                                '<span class="ml-2 p-2"> ' + this.name + ' </span>' +
                                '</div>' +
                                '</div>'
                        }
                    },
                    accessibility: {
                        point: {
                            valueSuffix: '%'
                        }
                    },
                    plotOptions: {
                        pie: {
                            allowPointSelect: true,
                            cursor: 'pointer',
                            dataLabels: {
                                enabled: false
                            },
                            states: {
                                hover: {
                                    brightness: 0.5
                                }
                            },
                            showInLegend: true,
                            point: {
                                events: {
                                    legendItemClick: (event) => {
                                        const name = event.target.name;
                                        const index = event.target.index;
                                        const isVisible = event.target.visible;

                                        this.$refs.chartOther.chart.series[0].data.forEach(point => {
                                            if (point.name === name) {
                                                point.update({
                                                    visible: !isVisible
                                                });
                                            }
                                        });
                                        this.$refs.chartOther.chart.series[1].data.forEach(point => {
                                            if (point.name === name) {
                                                point.update({
                                                    visible: !isVisible
                                                });
                                            }
                                        });
                                    },
                                    // mouseOver: function() {
                                    //     let relatedPoints = [],
                                    //         series = this.series,
                                    //         pIndex = this.index;
                                    //
                                    //     series.chart.series.forEach(function(s) {
                                    //         if (s !== series) {
                                    //             relatedPoints.push(s.points[pIndex]);
                                    //         }
                                    //     });
                                    //
                                    //     relatedPoints.forEach(function(p) {
                                    //         p.setState('hover');
                                    //     });
                                    // this.specieHover(event);
                                    // this.graphic.attr({
                                    //     fill: 'red'
                                    // });
                                    // }
                                }
                            }
                        },
                    },
                    series: [
                        {
                            id: 'Territoire',
                            name: 'Territoire',
                            size: '0',
                            // center: [500, 100],
                            // center: [250, 150],
                            data: []
                        }],
                    tooltip: {
                        backgroundColor: '#fff',
                        formatter: function() {
                            return'<b>' + this.key + '</b><br>' + '<b>' + this.y.toFixed(1).replace('.', ',') + ' % </b><br><b>'+ this.point.hectar +' ha</b>';
                        }
                    },
                },
                assolements: {},
                totalHaUser : 0,
                totalHaArea : 0,
            }
        },
        mounted() {
            this.getAssolements('user');
            this.getAssolements('area');
        },
        methods : {
            onVisibleOther() {
                this.visibleOther = !this.visibleOther;
                if(this.visibleOther) {

                    this.chartAssolementsOther.series.map(serie => {
                        serie.visible = true;
                    });
                } else {
                    this.chartAssolementsOther.series.map(serie => {
                        serie.visible = false;
                    });
                }
            },
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
                    cache: {ignoreCache: false}
                }).then((resp) => {
                    let totalHectar = 0;
                    resp.data.result.forEach(data => totalHectar += parseFloat(data.Sum));
                    let rankingAssolement = resp.data.result.sort((a, b) => (parseFloat(a.Sum) < parseFloat(b.Sum) ? 1 : -1));
                    this.assolements[by] = rankingAssolement;
                    this.assolements[by].hectars = totalHectar;
                    if(by == 'user') {
                        this.generateChart('user');
                    }
                    if(by == 'area') {
                        this.generateChart('area');
                    }
                });
            },
            generateChart(by) {
                //User data
                if(by == 'user') {
                    this.assolements[by].forEach((specie, index) => {
                        let hectars = parseFloat(specie.Sum);
                        let specieHectar = parseFloat(hectars);
                        let AVG = parseFloat(hectars/this.assolements[by].hectars*100);
                        let newLine = {};
                        this.totalHaUser += parseFloat(hectars);

                        this.chartAssolements.labels.items[0].html = 'Mon exploitation (' + this.totalHaUser +'ha)';
                        this.$set(this.chartAssolements.labels.items[0], 'html', 'Mon exploitation (' + this.totalHaUser +'ha)');
                    if(AVG > 2) {
                            this.chartAssolements.series[0].data.push({name: specie.Label, hectar : specieHectar.toFixed(1).replace('.', ','), y: AVG});
                            this.chartLegend.series[0].data.push({name: specie.Label, hectar : 0, y: 0}); // For legend
                        } else {
                            this.chartAssolementsOther.series.push({
                                'data': [AVG],
                                'hectars' : [specieHectar],
                                'name': specie.Label,
                                'id': specie.Label,
                                'visible' :  true,
                            });

                        let findObjOther = this.chartAssolements.series[0].data.find(obj => {
                                return obj.name == 'Autres';
                            });

                            if(findObjOther) {
                                findObjOther.hectar = (parseFloat(findObjOther.hectar.replace(',', '.')) + specieHectar).toFixed(1).replace('.', ',');
                                findObjOther.y +=  AVG;
                            } else {
                                newLine = {name: 'Autres', hectar : specieHectar.toFixed(1).replace('.', ','), y: AVG};
                                this.chartAssolements.series[0].data.push(newLine);
                                // this.chartLegendOther.series[0].data.push({name: specie.Label, hectar : 0, y: 0}); // For legend
                            }
                        }
                    });
                }

                //Area data
                if(by == 'area') {
                    this.assolements[by].forEach((specie, index) => {
                        let hectars = parseFloat(specie.Sum);
                        let specieHectar = parseFloat(hectars);
                        let AVG = parseFloat(hectars / this.assolements[by].hectars * 100);

                        this.totalHaArea += parseFloat(hectars);
                        this.chartAssolements.labels.items[1].html = 'Mon territoire (' + this.totalHaUser +'ha)';
                        this.$set(this.chartAssolements.labels.items[1], 'html', 'Mon territoire (' + this.totalHaUser +'ha)')

                        let specieExist = this.chartAssolements.series[1].data.find(obj => {
                            return obj.name === specie.Label
                        });
                        if(specieExist) {
                            specieExist.hectar = specieHectar.toFixed(1).replace('.', ',');
                            specieExist.y = AVG;
                        } else {
                            let newLine = {name: specie.Label, hectar: specieHectar.toFixed(1).replace('.', ','), y: AVG};
                            if(AVG > 2) {
                                this.chartAssolements.series[1].data.push(newLine);

                                let findDataBar = this.chartLegend.series[0].data.find(obj => {
                                    return obj.name == specie.Label;
                                });

                                if(!findDataBar) {
                                    this.chartLegend.series[0].data.push(newLine);
                                }
                            } else {

                                let findDataBar = this.chartAssolementsOther.series.find(obj => {
                                    return obj.id == specie.Label;
                                });

                                if(findDataBar) {
                                    findDataBar.data.push(specieHectar.toFixed(1).replace('.', ','))
                                } else {
                                    this.chartAssolementsOther.series.push({
                                        'data': [0,AVG],
                                        'hectars' : [0, specieHectar],
                                        'name': specie.Label,
                                        'id': specie.Label,
                                        'visible' :  true,
                                    });
                                }

                                let findObjOther = this.chartAssolements.series[1].data.find(obj => {
                                    return obj.name == 'Autres';
                                });

                                if(findObjOther) {
                                    findObjOther.hectar = (parseFloat(findObjOther.hectar.replace(',', '.')) + specieHectar).toFixed(1).replace('.', ',');
                                    findObjOther.y +=  AVG;
                                } else {
                                    newLine = {name: 'Autres', hectar : specieHectar.toFixed(1).replace('.', ','), y: AVG};
                                    this.chartAssolements.series[1].data.push(newLine);
                                    // this.chartAssolementsOther.series[1].data.push(newLine);
                                    this.chartLegendOther.series[0].data.push({name: specie.Label, hectar : 0, y: 0}); // For legend
                                }
                            }
                        }
                    });
                }
            },
            specieHover(partHover) {
                const name = partHover.target.name;
                const value = partHover.target.value;

                this.$refs.chart.chart.series[0].data.forEach((point, index) => {
                    if (point.name === name) {
                        point.setState('hover');
                        this.$refs.chart.chart.tooltip.refresh(point);
                        this.$refs.chart.chart.series[1].compare = {'serieName': point.series.userOptions.name, 'hectar': point.hectar, 'key': point.name, 'y': point.options.y};
                    }
                });

                this.$refs.chart.chart.series[1].data.forEach(point => {
                    if (point.name === name) {
                        point.setState('hover');
                        this.$refs.chart.chart.tooltip.refresh(point);
                        this.$refs.chart.chart.series[0].compare = {'serieName': point.series.userOptions.name, 'hectar': point.hectar, 'key': point.name, 'y': point.options.y};
                    }
                });
            },
            partClicked(part) {
                if(part.point.name == 'Autres') {
                    this.modalOther = true;
                }
            },
            legendClick(legend) {
                const name = legend.target.name;
                const isVisible = legend.target.visible;
                this.$refs.chart.chart.series[0].data.forEach(point => {
                    if (point.name === name) {
                        point.update({
                            visible: !isVisible
                        });
                    }
                });

                this.$refs.chart.chart.series[1].data.forEach(point => {
                    if (point.name === name) {
                        point.update({
                            visible: !isVisible
                        });
                    }
                });
            },
        },
        watch: {
            argsHref: function (newFarm, oldFarm) {
                this.chartAssolements.series[0].data = [];
                this.chartAssolementsOther.series[0] = [];
                this.chartAssolementsOther.series[1] = [];
                this.getAssolements('user');
                this.getAssolements('area');
            },
        },
    }

</script>
<style lang="scss" scoped>
  @import "../style/theme";
  @import "../style/navbar";
  #modalOther {
    background: #fff;
    border-radius: 5px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
  }
.titleModalOther {
  background: #fff;
  font-weight: 800;
  color: #3d7b60;
  border-bottom: 1px solid #e3e3e3;
  text-align: center;
  padding: 5px;
  font-size: 17px;
}
  .modal-body {
    .fa-close {
      right: 18px;
      top: 18px;
      position: absolute;
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

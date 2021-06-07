<template>
  <section class="container">

    <!-- MODAL -->
    <div class="modal fade" data-backdrop="static" :class="[modalExportPDF ? 'show' : '']" id="exportPDF" tabindex="-1"
         role="dialog" aria-labelledby="modalDistance" aria-hidden="true">
      <div class="modal-dialog modal-xxl" role="document">
        <div class="modal-content text-dark bg-white border-0">
          <div class="modal-header bg-primary">
            <div class="modal-title font-weight-bold" id="exampleModalLabel">Gestion de la distance</div>
          </div>
          <div class="modal-body">
            <div class="container-fluid">
              <div class="row">
                <div class="text-lg my-3">Veuillez sélectionner les informations à exporter :</div>
                <div class="col-md-6">
                  <label class="check">
                    <input type="checkbox" id="yieldInquireCounter" value="yieldInquireCounter" v-model="pdfOptions"/>
                    <div class="box border"></div>
                    <span class="checkox-label font-weight-normal">Rendement</span>
                  </label>
                </div>
                <div class="col-md-6">
                  <label class="check">
                    <input type="checkbox" id="varietyLabelCounter" value="varietyLabelCounter" v-model="pdfOptions"/>
                    <div class="box border"></div>
                    <span class="checkox-label font-weight-normal">Variété</span>
                  </label>
                </div>
                <div class="col-md-6">
                  <label class="check">
                    <input type="checkbox" id="sowindDateInquireCounter" value="sowindDateInquireCounter" v-model="pdfOptions"/>
                    <div class="box border"></div>
                    <span class="checkox-label font-weight-normal">Date semis</span>
                  </label>
                </div>
                <div class="col-md-6">
                  <label class="check">
                    <input type="checkbox"/>
                    <div class="box border"></div>
                    <span class="checkox-label font-weight-normal" id="soilType" value="soilType" v-model="pdfOptions">Type de sol</span>
                  </label>
                </div>
                <div class="col-md-6">
                  <label class="check">
                    <input type="checkbox" id="interventionCounter" value="interventionCounter" v-model="pdfOptions"/>
                    <div class="box border"></div>
                    <span class="checkox-label font-weight-normal">Nombre d'interventions</span>
                  </label>
                </div>
              </div>
              <div>
              </div>
            </div>
          </div>
          <div class="modal-footer d-flex align-items-center justify-content-center">
            <div @click="modalExportPDF = !modalExportPDF" type="button" class="btn text-md btn-terciary px-5 m-3"
                 data-dismiss="modal">Annuler
            </div>
            <div @click="uploadPdf" type="button" class="btn text-md btn-primary px-5 m-3">Exporter</div>
          </div>
        </div>
      </div>
    </div>

    <data-lab-filter-species :optionSpecies="optionSpecies" :selectedSpecies="selectedSpecies" @set-specie-picked="setSpeciePicked"></data-lab-filter-species>

    <div class="row" id="uploadPagePdf">
      <div v-if="speciesbyYear.length" class="col-md-12">
        <div class="panel-group mt-5 uiv">
            <div class="panel panel-default panel_collapse">
              <div class="panel-heading d-flex align-items-center" role="button">
                <div class="col-md-2 panel-title text-white text-center text-lg bg-primary p-5">
                  <div>Rendements</div>
                  <div class="panel-entry-rating mt-3">
                    <i :class="[getPercentage(speciesbyYear, 'yieldInquireCounter') > 25 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                    <i :class="[getPercentage(speciesbyYear, 'yieldInquireCounter') > 50 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                    <i :class="[getPercentage(speciesbyYear, 'yieldInquireCounter') > 75 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                    <i :class="[getPercentage(speciesbyYear, 'yieldInquireCounter') == 100 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                  </div>
                </div>
                <div class="panel-body col-md-10 d-flex justify-content-between">
                  <div v-for="specieYears in speciesbyYear"  class="panel-entry-year p-3 mx-2 text-center">
                    <div class="text-dark text-lg">{{specieYears.cropYear}}</div>
                    <div class="separator bg-primary mb-3 mt-2"></div>
                    <div class="dataLab-dataEntry p-3 d-flex align-items-center">
                      <div class="dataLab-dataEntry-fill" :style="{right: 100 - specieYears.yieldInquireCounter/specieYears.counter * 100 + '%'}">
                      </div>
                      <animated-number
                        class="progress-bar-text text-primary text-lg"
                        :value="specieYears.yieldInquireCounter/specieYears.counter * 100"
                        :formatValue="val => val.toFixed(0)+'%'"
                        :duration="800"
                      />
                    </div>
                    <div></div>
                  </div>
                </div>
              </div>

              <collapse v-model="showAccordion[0]">
                <div class="panel-body">
                  <table class="table text-primary table-bordered table-hover table_entry">
                    <tbody v-for="(cropsYear, index) in currentCrops">
                      <tr>
                        <td style="width: 10%">{{cropsYear[0].label}}</td>
                        <td v-for="currentYear in yearsAvaiable" style="width: 12%">
                            <div :style= "getCropColor(cropsYear, 'yieldInquireCounter', currentYear)" style="height: 28px; width:28px; borderRadius:50%; margin:auto;"></div>
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </collapse>
              <div class="dataLab-panel-buttonDetail">
                <div @click="toggleAccordion(0)" type="button" class="btn bg-secondary text-white px-4">
                  <span class="text-md">{{showAccordion[0] ? 'Masquer le détail' : 'Afficher le detail'}}</span>
                </div>
              </div>
              <div @click="modalExportPDF = !modalExportPDF" data-toggle="modal" data-target="#exportPDF"
                   class="dataLab-panel-buttonExport">
                <div type="button" class="btn bg-terciary text-white px-5">
                  <span class="text-md">Export</span>
                </div>
              </div>
            </div>
          </div>


          <div class="panel panel-default panel_collapse">
          <div class="panel-heading d-flex align-items-center" role="button">
            <div class="col-md-2 panel-title text-white text-center text-lg bg-primary p-5">
              <div>Date de semis de mes grandes cultures</div>
              <div class="panel-entry-rating mt-3">
                <i :class="[getPercentage(speciesbyYear, 'sowindDateInquireCounter') > 25 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                <i :class="[getPercentage(speciesbyYear, 'sowindDateInquireCounter') > 50 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                <i :class="[getPercentage(speciesbyYear, 'sowindDateInquireCounter') > 75 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                <i :class="[getPercentage(speciesbyYear, 'sowindDateInquireCounter') == 100 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
              </div>
            </div>
            <div class="panel-body col-md-10 d-flex justify-content-between">
              <div v-for="specieYears in speciesbyYear"  class="panel-entry-year p-3 mx-2 text-center">
                <div class="text-dark text-lg">{{specieYears.cropYear}}</div>
                <div class="separator bg-primary mb-3 mt-2"></div>
                <div class="dataLab-dataEntry p-3 d-flex align-items-center">
                  <div class="dataLab-dataEntry-fill" :style="{right: 100 - specieYears.sowindDateInquireCounter/specieYears.counter * 100 + '%'}">
                  </div>
                  <animated-number
                    class="progress-bar-text text-primary text-lg"
                    :value="specieYears.sowindDateInquireCounter/specieYears.counter * 100"
                    :formatValue="val => val.toFixed(0)+'%'"
                    :duration="800"
                  />
                </div>
                <div></div>
              </div>
            </div>
          </div>
            <collapse v-model="showAccordion[1]">
            <div class="panel-body">
              <table class="table text-primary table-bordered table-hover table_entry">
                <tbody v-for="(cropsYear, index) in currentCrops">
                <tr>
                  <td style="width: 10%">{{cropsYear[0].label}}</td>
                  <td v-for="currentYear in yearsAvaiable" style="width: 12%">
                    <div :style= "getCropColor(cropsYear, 'sowindDateInquireCounter', currentYear)" style="height: 28px; width:28px; borderRadius:50%; margin:auto;"></div>
                  </td>
                </tr>
                </tbody>
              </table>
            </div>
          </collapse>
          <div class="dataLab-panel-buttonDetail">
            <div @click="toggleAccordion(1)" type="button" class="btn bg-secondary text-white px-4">
              <span class="text-md">{{showAccordion[1] ? 'Masquer le détail' : 'Afficher le detail'}}</span>
            </div>
          </div>
          <div @click="modalExportPDF = !modalExportPDF" data-toggle="modal" data-target="#exportPDF"
               class="dataLab-panel-buttonExport">
            <div type="button" class="btn bg-terciary text-white px-5">
              <span class="text-md">Export</span>
            </div>
          </div>
        </div>

          <div class="panel panel-default panel_collapse">
          <div class="panel-heading d-flex align-items-center" role="button">
            <div class="col-md-2 panel-title text-white text-center text-lg bg-primary p-5">
              <div>Variétés de mes grandes cultures</div>
              <div class="panel-entry-rating mt-3">
                <i :class="[getPercentage(speciesbyYear, 'varietyLabelCounter') > 25 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                <i :class="[getPercentage(speciesbyYear, 'varietyLabelCounter') > 50 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                <i :class="[getPercentage(speciesbyYear, 'varietyLabelCounter') > 75 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
                <i :class="[getPercentage(speciesbyYear, 'varietyLabelCounter') == 100 ? 'text-secondary' : '']" class="fa fa-star mx-1"></i>
              </div>
            </div>
            <div class="panel-body col-md-10 d-flex justify-content-between">
              <div v-for="specieYears in speciesbyYear"  class="panel-entry-year p-3 mx-2 text-center">
                <div class="text-dark text-lg">{{specieYears.cropYear}}</div>

                <div class="separator bg-primary mb-3 mt-2"></div>
                <div class="dataLab-dataEntry p-3 d-flex align-items-center">
                  <div class="dataLab-dataEntry-fill" :style="[!specieYears.varietyLabel.includes('sans variété') ? {right: 100 - specieYears.varietyLabelCounter/specieYears.counter * 100 +'%'} : {right: 100+'%'}]">
                  </div>
                  <animated-number
                    class="progress-bar-text text-primary text-lg"
                    :value="!specieYears.varietyLabel.includes('sans variété') ? specieYears.varietyLabelCounter/specieYears.counter * 100 : 0"
                    :formatValue="val => val.toFixed(0)+'%'"
                    :duration="800"
                  />
                </div>
                <div></div>
              </div>
            </div>
          </div>
          <collapse v-model="showAccordion[2]">
            <div class="panel-body">
              <table class="table text-primary table-bordered table-hover table_entry">
                <tbody v-for="(cropsYear, index) in currentCrops">
                <tr>
                  <td style="width: 10%">{{cropsYear[0].label}}</td>
                  <td v-for="currentYear in yearsAvaiable" style="width: 12%">
                    <div :style= "getCropColor(cropsYear, 'varietyLabelCounter', currentYear)" style="height: 28px; width:28px; borderRadius:50%; margin:auto;"></div>
                  </td>
                </tr>
                </tbody>
              </table>
            </div>
          </collapse>
          <div class="dataLab-panel-buttonDetail">
            <div @click="toggleAccordion(2)" type="button" class="btn bg-secondary text-white px-4">
              <span class="text-md">{{showAccordion[2] ? 'Masquer le détail' : 'Afficher le detail'}}</span>
            </div>
          </div>
          <div @click="modalExportPDF = !modalExportPDF" data-toggle="modal" data-target="#exportPDF" class="dataLab-panel-buttonExport">
            <div type="button" class="btn bg-terciary text-white px-5">
              <span class="text-md">Export</span>
            </div>
          </div>
        </div>

          <div class="panel panel-default panel_collapse mb-5 mt-3">
            <div class="panel-heading d-flex align-items-center" role="button">
              <div class="col-md-2 panel-title text-white text-center text-lg bg-primary p-5">
                <div>Nombre d'interventions</div>
              </div>
              <div class="panel-body col-md-10 d-flex justify-content-between">
                <div v-for="specieYears in speciesbyYear"  class="panel-entry-year p-3 mx-2 text-center">
                  <div class="text-dark text-lg">{{specieYears.cropYear}}</div>
                  <div class="separator bg-primary mb-3 mt-2"></div>
                  <div class="card-title m-0 text-secondary text-lg font-weight-bold">
                    <animated-number
                      :value="specieYears.interventionCounter"
                      :formatValue="val => val.toFixed(0)"
                      :duration="800"
                    />
                  </div>

                  <div></div>
                </div>
              </div>
            </div>
            <div @click="modalExportPDF = !modalExportPDF" data-toggle="modal" data-target="#exportPDF"
                 class="dataLab-panel-buttonExport">
              <div type="button" class="btn bg-terciary text-white px-5">
                <span class="text-md">Export</span>
              </div>
            </div>
          </div>
        </div>
    </div>
  </section>
</template>
<script>
    import {Collapse, ProgressBar} from 'uiv'
    import AnimatedNumber from "animated-number-vue";
    import DataLabFilterSpecies from "../components/DataLabFilterSpecies";

    export default {
        name: "DataLabEntryDetails",
        components: {
            Collapse,
            ProgressBar,
            AnimatedNumber,
            DataLabFilterSpecies
        },
        props: {
            user: Object,
            argsHref: Array,
            topSpecieId: Number,
        },
        data() {
            return {
                showFilter: false,
                showAccordion: [false, false, false, false],
                progress: 0,
                modalExportPDF: false,
                species : [],
                optionSpecies: {},
                selectedSpecies : null,
                speciesbyYear : [],
                inquireByYears : {},
                currentCrops : [],
                yearsAvaiable : [],
                pdfOptions : [
                    'yieldInquireCounter',
                    'varietyLabelCounter',
                    'sowindDateInquireCounter',
                    'interventionCounter',
                    'soilType'
                ],
            }
        },
        methods: {
            setSpeciePicked(specie) {
                this.selectedSpecies = specie;
                this.speciesbyYear = [];
                this.getEntrySpecie();
            },
            getCropColor(crops, type, currentYear) {

                let findCrop = crops.find(crop => crop.year == currentYear);
                if(findCrop && !findCrop.labelSpecie.includes('sans variété')) {
                    if(findCrop[type]/findCrop.counter*100 < 33) {
                        return {'background':'#98242D'}
                    }
                    if(findCrop[type]/findCrop.counter*100 < 66) {
                        return {'background':'#F29B34'}
                    } else {
                        return {'background':'#CDD52C'}
                    }
                } else {
                    return {'background':'#b3b4b3'}
                }
            },
            getPercentage(array, element) {
                let counterElement = 0,
                    counter = 0;
                array.forEach(data => {
                    if(element == 'varietyLabelCounter' && data.varietyLabel.includes('sans variété')) {
                        counterElement += 0;
                    } else {
                        counterElement += data[element];
                    }
                    counter += data.counter
                });
                return counterElement/counter * 100;
            },
            toggleAccordion(index) {
                if (this.showAccordion[index]) {
                    this.$set(this.showAccordion, index, false)
                } else {
                    this.showAccordion = this.showAccordion.map((v, i) => i === index)
                }
            },
            groupBy(myArray, group) {
                const arrayHashmap = myArray.reduce((obj, item) => {
                    let groupBy;
                    if(item[group] != undefined) {
                        groupBy = item[group];
                    } else {
                        groupBy = item.cropZone[group];
                    }
                    if(obj.hasOwnProperty(groupBy)) {
                        let dataInquire = 0,
                            yieldInquireCounter = 0,
                            sowindDateInquireCounter = 0,
                            varietyLabelCounter = 0;

                        if (item.cropZone.isCorrectYield && item.cropZone.isCorrectSowingDate && item.cropZone.area && item.varietyLabel) {
                            obj[groupBy].dataInquire ++;
                            dataInquire++;
                        }
                        if(item.cropZone.isCorrectYield) {
                            obj[groupBy].yieldInquireCounter ++;
                            yieldInquireCounter ++;
                        }
                        if(item.cropZone.isCorrectSowingDate) {
                            obj[groupBy].sowindDateInquireCounter ++;
                            sowindDateInquireCounter ++;
                        }
                        if(item.varietyLabel) {
                            obj[groupBy].varietyLabelCounter ++;
                            varietyLabelCounter ++;
                        }
                        item.interventionCounter += item.cropZone.otherTypeOperationCount + item.cropZone.sowingAndPlantingOperationCount + item.cropZone.manureFertilizingOperationCount + item.cropZone.otherFertilizingOperationCount + item.cropZone.herbicideFertilizingOperationCount+ item.cropZone.fungicidegOperationCount + item.cropZone.insecticideOperationCount + item.cropZone.regulateurOperationCount + item.cropZone.harvestOperationCount + item.cropZone.supplyOperationCount;

                        obj[groupBy].counter++;

                        let findCrop = obj[groupBy].crops.find(crop => crop.id == item.cropId);

                        if(findCrop != undefined && findCrop) {
                            if (item.cropZone.isCorrectYield && item.cropZone.isCorrectSowingDate && item.cropZone.area && item.varietyLabel) {
                                findCrop.dataInquire ++
                            }
                            if(item.cropZone.isCorrectYield) {
                                findCrop.yieldInquireCounter ++;
                            }
                            if(item.cropZone.isCorrectSowingDate) {
                                findCrop.sowindDateInquireCounter ++;
                            }
                            if(item.varietyLabel) {
                                findCrop.varietyLabelCounter ++;
                            }
                            findCrop.interventionCounter += item.cropZone.otherTypeOperationCount + item.cropZone.sowingAndPlantingOperationCount + item.cropZone.manureFertilizingOperationCount + item.cropZone.otherFertilizingOperationCount + item.cropZone.herbicideFertilizingOperationCount+ item.cropZone.fungicidegOperationCount + item.cropZone.insecticideOperationCount + item.cropZone.regulateurOperationCount + item.cropZone.harvestOperationCount + item.cropZone.supplyOperationCount;
                            findCrop.counter ++;
                        } else {
                            obj[groupBy].crops.push({
                                'id' : item.cropId,
                                'labelSpecie' : item.varietyLabel,
                                'label' : item.cropLabel,
                                'dataInquire' : dataInquire,
                                'yieldInquireCounter' : yieldInquireCounter,
                                'sowindDateInquireCounter' : sowindDateInquireCounter,
                                'varietyLabelCounter': varietyLabelCounter,
                                'interventionCounter':
                                    item.cropZone.otherTypeOperationCount +
                                    item.cropZone.sowingAndPlantingOperationCount +
                                    item.cropZone.manureFertilizingOperationCount +
                                    item.cropZone.otherFertilizingOperationCount +
                                    item.cropZone.herbicideFertilizingOperationCount +
                                    item.cropZone.fungicidegOperationCount +
                                    item.cropZone.insecticideOperationCount +
                                    item.cropZone.regulateurOperationCount +
                                    item.cropZone.harvestOperationCount +
                                    item.cropZone.supplyOperationCount,
                                'year' : item.cropZone.cropYear,
                                'counter' : 1
                            })
                        }
                    } else {
                        item.dataInquire = 0;
                        if (item.cropZone.isCorrectYield && item.cropZone.isCorrectSowingDate && item.cropZone.area && item.varietyLabel) {
                            item.dataInquire = 1;
                        }
                        item.yieldInquireCounter = 0;
                        if(item.cropZone.isCorrectYield) {
                            item.yieldInquireCounter = 1;
                        }
                        item.sowindDateInquireCounter = 0;
                        if(item.cropZone.isCorrectSowingDate) {
                            item.sowindDateInquireCounter = 1;
                        }
                        item.varietyLabelCounter = 0;
                        if(item.varietyLabel) {
                            item.varietyLabelCounter = 1;
                        }
                        item.interventionCounter = item.cropZone.otherTypeOperationCount + item.cropZone.sowingAndPlantingOperationCount + item.cropZone.manureFertilizingOperationCount + item.cropZone.otherFertilizingOperationCount + item.cropZone.herbicideFertilizingOperationCount+ item.cropZone.fungicidegOperationCount + item.cropZone.insecticideOperationCount + item.cropZone.regulateurOperationCount + item.cropZone.harvestOperationCount + item.cropZone.supplyOperationCount;

                        item.counter = 1;
                        item.cropYear = item.cropZone.cropYear;

                        item.crops = [{
                            'id' : item.cropId,
                            'label' : item.cropLabel,
                            'labelSpecie' : item.varietyLabel,
                            'dataInquire' : item.dataInquire,
                            'yieldInquireCounter' : item.yieldInquireCounter,
                            'sowindDateInquireCounter' : item.sowindDateInquireCounter,
                            'varietyLabelCounter': item.varietyLabelCounter,
                            'interventionCounter': item.interventionCounter,
                            'year' : item.cropZone.cropYear,
                            'counter' : 1
                        }]

                        obj[groupBy] = item;
                    }
                    return obj;
                }, {});
                return Object.values(arrayHashmap);
            },
            getSpecies() {
                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    "params": {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCustomId",
                        "params": {
                            "customId": suConfig.subscriber.customId,
                        },
                    },
                    cache: {ignoreCache: true, maxAge : 60 * 60 * 1000 * 12, exclude: {query: false}}
                }).then((resp) => {
                    this.species = resp.data.result;
                    let speciesByspecie = this.species.filter((v,i,a)=>a.findIndex(t=>(t.cropSpeciesId === v.cropSpeciesId))===i)
                    let preOptionSpecies = [];


                    speciesByspecie = speciesByspecie.sort((a, b) => (a.label > b.label) ? 1 : -1);


                    speciesByspecie.forEach(specie => {
                      preOptionSpecies.push({
                          id: specie.cropSpeciesId,
                          label : specie.cropLabel,
                          labelSpecie: specie.cropSpeciesLabel,
                          season: specie.cropSeasonLabel
                        });
                    });

                    if(this.topSpecieId) {
                        let specie1 = speciesByspecie.filter(specie => specie.cropSpeciesId == this.topSpecieId);
                        this.selectedSpecies = {
                            'id' : specie1[0].cropSpeciesId,
                            'label' : specie1[0].cropLabel,
                            'labelSpecie' : specie1[0].cropSpeciesLabel,
                            'season': specie1[0].cropSeasonLabel
                        }
                    } else {
                        this.selectedSpecies = {
                            'id' : speciesByspecie[0].cropSpeciesId,
                            'label' : speciesByspecie[0].cropLabel,
                            'labelSpecie' : speciesByspecie[0].cropSpeciesLabel,
                            'season': speciesByspecie[0].cropSeasonLabel
                        }
                    }

                    const arrayHashmap = preOptionSpecies.reduce((obj, item) => {
                        if(obj[item.season]) {
                            obj[item.season].push(item);
                        } else{
                            item.season = item.season;
                            (obj[item.season] = [{ ...item }]);
                        }
                        return obj;
                    }, {});
                    this.optionSpecies = arrayHashmap;

                    this.getEntrySpecie();
                });
            },
            getEntrySpecie() {
                let specieFiltered = this.species.filter(specie => specie.cropSpeciesId == this.selectedSpecies.id);
                this.speciesbyYear = this.groupBy(specieFiltered, 'cropYear');
                this.speciesbyYear = this.speciesbyYear.slice(2, this.speciesbyYear.length);

                let crops = []
                this.yearsAvaiable = [];

                this.speciesbyYear.forEach(specieYear => {
                    crops.push(specieYear.crops);
                    if(!this.yearsAvaiable.includes(specieYear.cropYear)) {
                        this.yearsAvaiable.push(specieYear.cropYear);
                    }
                });


                crops = crops.flat();

                const arrayHashmap = crops.reduce((obj, item) => {
                    let groupBy = item.id;
                    if(obj.hasOwnProperty(groupBy)) {
                        obj[groupBy].push(item);
                    } else {
                        obj[groupBy] = [item];

                    }
                    return obj;
                }, {});

                let valueCrops = Object.values(arrayHashmap);

                this.currentCrops = valueCrops;
            },
            uploadPdf() {
                // console.log(this.currentCrops, this.pdfOptions)
                let filteredCrop = JSON.parse(JSON.stringify(this.currentCrops))
                if(this.pdfOptions.length){
                    this.pdfOptions.forEach(option => {
                        filteredCrop.forEach(crops => {
                            crops.forEach(crop => {
                                // console.log(crop, option)
                                delete crop[option];
                            })
                        });
                    });
                    // let filteredCrops = this.currentCrops.filter(crop => {
                    //     crop.
                    // });
                }
                return
                let apiInternal = new AxiosApiInternal({username: apiUsername, password: apiToken});
                apiInternal.login().then(() => {
                    let fd = new FormData()
                    fd.append('data', JSON.stringify( {
                        automation: 'ayfSU9EdLsiDumrcZACRKgtiUSM8UUIv1YmIFJVg6wu5nyodBcj4AFVYnqp2NAABP@job.send-up.net',
                        sheet: filteredCrop
                    }));
                    apiInternal.post('/Automation/action', fd).then(() => {
                    });
                });


                // apiInternal = new AxiosApiInternal({username: apiUsername, password: apiToken});
                // apiInternal.login().then(() => {
                //
                // });
            },
        },
        mounted() {
          // console.log(html2pdf);
          this.getSpecies();
        }
    }

</script>

<style lang="scss" scoped>
  @import "../style/theme";

  .table_entry {
    td {
      padding: 21px;
      padding-bottom: 13px;
    }
  }

  .dataLab-panel-buttonDetail {
    left: 5%;
    position: absolute;

    .btn {
      border-radius: 0px 0px 10px 10px;
    }
  }

  .dataLab-panel-buttonExport {
    right: 14px;
    position: absolute;

    .btn {
      border-radius: 0px 0px 10px 10px;
    }
  }

  .panel-entry-year {
    flex: 1;
  }

  .panel-entry-rating {
    .fa {
      font-size: 21px;
    }
  }

  .dataLab-dataEntry {
    background: $grey;
    height: 8px;
    position: relative;
    overflow: hidden;
    display: flex;
    justify-content: center;
    border-radius: 12px;

    .dataLab-dataEntry-fill {
      transition: 0.8s ease-in-out all;
      border-radius: 12px;
      background: $secondary-color;
      position: absolute;
      right: 0;
      bottom: 0;
      top: 0;
      right: 0;
      left: 0;
      height: 100%;
    }
  }

  .panel_collapse {
    background-color: $white;
    border-radius: 10px 10px 0px 10px;
    box-shadow: none;
    overflow: hidden;
    border: none;
    margin-bottom: 5rem;

    .panel-heading {
      background-color: $white;
      padding: 0;
    }
  }

</style>

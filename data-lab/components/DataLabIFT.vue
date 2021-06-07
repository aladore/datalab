<template>
  <section class="col-md-6">
    <div class="card card_datalab">
      <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
        <div class="card-header-title">IFT <span> - {{argsHref[0]}}</span></div>
        <a class="bg-secondary card-header-button" :key="links.IFT.route" :href="links.IFT.route" :farming="farming">En savoir +</a>
      </div>
      <div class="card-body">
        <div class="dataLab-panel-IFT rounded-2 py-2 my-2 d-flex justify-content-around align-items-center">
          <div class="dataLab-panel-IFT-caption text-center">
            <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/User-2.svg" width="48px" height="48px">
            <div class="text-primary">Moi</div>
          </div>
          <div class="dataLab-panel-IFT-data text-center border-right border-left p-3 border-primary">
            <div class="card-title m-0 text-secondary font-weight-bold">
              <animated-number
                :value="cropsUser.cropsTotal"
                :formatValue="val => val.toFixed(0)"
                :duration="800"
              />
            </div>
            <div class="card-text text-primary dataLab-IFT-plot-text">Parcelles renseignées</div>
          </div>
          <div class="dataLab-panel-IFT-data text-center">
            <div class="card-title m-0 text-secondary font-weight-bold">
              <animated-number
                v-if ="cropsUser.cropsInquireGC"
                :value="cropsUser.cropsInquireGC"
                :formatValue="val => val.toFixed(1).replace('.', ',')"
                :duration="800"
              />
              <div v-else class="text-secondary font-weight-bold">0</div>
            </div>
            <div class="card-text">Grandes cultures</div>
          </div>
          <div class="dataLab-panel-IFT-data text-center">
            <div class="card-title m-0 text-secondary font-weight-bold">
              <animated-number
                v-if ="cropsUser.cropsInquireVigne"
                :value="cropsUser.cropsInquireVigne"
                :formatValue="val => val.toFixed(1).replace('.', ',')"
                :duration="800"
              />
              <div v-else class="text-secondary font-weight-bold">0</div>
            </div>
            <div class="card-text">Vignes</div>
          </div>
        </div>

        <div class="dataLab-panel-IFT rounded-2 py-2 my-3 d-flex justify-content-around align-items-center">
          <div class="dataLab-panel-IFT-caption text-center">
            <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Map.svg" width="48px" height="48px">
            <div class="text-primary">Échantillon de référence*</div>
          </div>
          <div class="dataLab-panel-IFT-data text-center border-right border-left p-3 border-primary">
            <div class="card-title m-0 text-secondary font-weight-bold">
<!--              :value="cropsArea.cropsInquire"-->
              <animated-number
                :value="cropsArea.cropsTotal"
                :formatValue="val => val.toFixed(0).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ')"
                :duration="800"
              />
            </div>
            <div class="card-text text-primary dataLab-IFT-plot-text">Parcelles renseignées</div>
          </div>
          <div class="dataLab-panel-IFT-data text-center">
            <div class="card-title m-0 text-secondary font-weight-bold">
              <animated-number
                v-if ="cropsArea.cropsInquireGC"
                :value="cropsArea.cropsInquireGC"
                :formatValue="val => val.toFixed(1).replace('.', ',')"
                :duration="800"
              />
              <div v-else class="text-secondary font-weight-bold">0</div>
            </div>
            <div class="card-text">Grandes cultures</div>
          </div>
          <div class="dataLab-panel-IFT-data text-center">
            <div class="card-title m-0 text-secondary font-weight-bold">
              <animated-number
                v-if ="cropsArea.cropsInquireVigne"
                :value="cropsArea.cropsInquireVigne"
                :formatValue="val => val.toFixed(1).replace('.', ',')"
                :duration="800"
              />
              <div v-else class="text-secondary font-weight-bold">0</div>
            </div>
            <div class="card-text">Vignes</div>
          </div>
        </div>
        <div class="font-weight-light text-sm text-muted">Echantillon de référence* : exploitation références ayant saisies toutes leurs interventions</div>
      </div>
    </div>
  </section>
</template>

<script>
    import AnimatedNumber from "animated-number-vue";

    export default {
        name: "DataLabIFT",
        components: {
            AnimatedNumber,
        },
        props : {
            farming : Object,
            default : {},
            user : Object,
            argsHref : Array,
        },
        data() {
            return {
                links: {
                    'IFT' : {route: '#IFT/'+ this.argsHref[0]+'/'+this.argsHref[1]+'/'+this.argsHref[2]+'/'+this.argsHref[3], name: "DataLabIFT"}
                },
                cropsUser : {
                    cropsInquireGC : 0,
                    cropsInquireVigne: 0,
                    cropsTotal: 0,
                },
                cropsArea : {
                    cropsInquireGC : 0,
                    cropsInquireVigne: 0,
                    cropsTotal: 0,
                },
            }
        },
        mounted() {
            this.getIFT('user');
            this.getIFT('area');
        },
        methods : {
            getIFT(by) {
                let params = {};
                params.CropYear = parseInt(this.argsHref[0]);
                params.Type = 'iftTotal';
                if(by == 'user') {
                    params.customId = suConfig.subscriber.customId;
                }

                axiosSuApi.get(suConfig.datalab.baseUrl, {
                    params : {
                        "cfg": suConfig.datalab.cfg,
                        "class": "DatalabCropZoneEntity",
                        "method": "findByCropZoneIft",
                        params,
                    },
                    cache: {ignoreCache: true}
                }).then((resp) => {
                    if(by == 'area') {
                        resp.data.result.forEach(data => {
                            if(data.CropLabel == 'GC') {
                                this.cropsArea.cropsInquireGC = data.Total;
                                if(this.cropsArea.cropsTotal < parseFloat(data.AreaCount)) {
                                    this.cropsArea.cropsTotal = parseFloat(data.AreaCount);
                                }
                            }
                            if(data.CropLabel == 'Vigne') {
                                this.cropsArea.cropsInquireVigne = data.Total;
                                if(this.cropsArea.cropsTotal < parseFloat(data.AreaCount)) {
                                    this.cropsArea.cropsTotal = parseFloat(data.AreaCount);
                                }
                            }
                        });
                    }

                    if(by == 'user') {
                        resp.data.result.forEach(data => {
                            if(data.CropLabel == 'GC') {
                                this.cropsUser.cropsInquireGC = data.Total
                                if(this.cropsUser.cropsTotal < parseFloat(data.AreaCount)) {
                                    this.cropsUser.cropsTotal  = parseFloat(data.AreaCount);
                                }
                            }
                            if(data.CropLabel == 'Vigne') {
                                this.cropsUser.cropsInquireVigne = data.Total
                                if(this.cropsUser.cropsTotal  < parseFloat(data.AreaCount)) {
                                    this.cropsUser.cropsTotal  = parseFloat(data.AreaCount);
                                }
                            }
                        });
                    }
                })
            },
        },
        watch: {
          argsHref: function (newFarm, oldFarm) {
              this.links.IFT = {route: '#IFT/'+ this.argsHref[0]+'/'+this.argsHref[1]+'/'+this.argsHref[2]+'/'+this.argsHref[3], name: "DataLabAssolementDetails"};

              this.cropsUser = {
                  cropsInquireGC : 0,
                  cropsInquireVigne: 0,
                  cropsTotal: 0,
              },
              this.cropsArea = {
                  cropsInquireGC : 0,
                  cropsInquireVigne: 0,
                  cropsTotal: 0,
              },
              this.getIFT('user');
              this.getIFT('area');
          }
        }
    }
</script>

<style lang="scss" scoped>
  @import "../style/theme";

</style>

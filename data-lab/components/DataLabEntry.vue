<template>
  <section class="col-md-6">
    <div class="card card_datalab">
      <div class="card-header card-header_graph bg-primary d-flex justify-content-between">
        <div class="card-header-title">Qualité de mes saisies  <span> - {{this.argsHref[0]}}</span></div>
        <a class="bg-secondary card-header-button" :key="links.entry.route" :href="links.entry.route">En savoir +</a>
      </div>
      <div class="card-body">
        <div class="d-flex align-items-center p-3">
          <div class="text-secondary text-xxl font-weight-bold mr-3" style="z-index: 999;">
            <animated-number
              :value="inquirePercentage"
              :formatValue="val => val.toFixed(0)+'%'"
              :duration="800"
            />
          </div>
          <div style="line-height: 17px">
            <div class="text-lg font-weight-bold">de données saisies</div>
            <div class="text-md">encore un petit effort pour avoir une analyse complète.</div>
          </div>
        </div>
        <div class="dataLabFlowers">
          <div class="dataLabFlowers-grey" :style="{left:inquirePercentage + '%'}"></div>
          <div class="d-flex p-3 justify-content-between align-content-end">
            <div class="dataLabFlowers-containerImg">
              <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Plant-0-Color.svg"/>
            </div>
            <div class="dataLabFlowers-containerImg">
              <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Plant-1-Color.svg"/>
            </div>
            <div class="dataLabFlowers-containerImg">
            <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Plant-2-Color.svg"/>
            </div>
            <div class="dataLabFlowers-containerImg">
            <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Plant-3-Color.svg"/>
            </div>
            <div class="dataLabFlowers-containerImg">
              <img src="https://s3-eu-west-1.amazonaws.com/statics.ocealia-groupe.fr/Public/DataLab/Plant-4-Color.svg"/>
            </div>
          </div>
        </div>
        <div class="col-md-12">
          <div class="dataLab-dataEntry">
            <div class="dataLab-dataEntry-fill text-secondary" :style="{right: 100 - inquirePercentage + '%'}"></div>
          </div>
          <div class="font-weight-light text-sm text-muted text-center">votre progression</div>
        </div>
      </div>
    </div>
  </section>
</template>
<script>

    import AnimatedNumber from "animated-number-vue";

    export default {
        name: "DataLabDataEntry",
        components: {
            AnimatedNumber,
        },
        props: {
            argsHref : Array
        },
        data() {
            return {
                inquirePercentage: 0,
                links: {
                    'entry': {route: "#entry", name: "DataLabEntry"}
                },
            }
        },
        mounted() {
            this.getCropZoneByCustomId();
        },
        methods: {
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
                    let entryOk = 0;
                    resp.data.result.forEach(data => {
                        if (data.cropZone.isCorrectYield && data.cropZone.isCorrectSowingDate && data.cropZone.area && data.varietyLabel) {
                            entryOk ++
                        }
                    })
                    this.inquirePercentage = (entryOk/resp.data.result.length)*100;
                })
            },
        },
        watch: {
            argsHref: function (newFarming, oldFarming) {
                this.getCropZoneByCustomId();
            },
        }
    }


</script>
<style lang="scss" scoped>
  @import "../style/theme";
  /* Quantité saisies */
  .dataLabFlowers {
    position: relative;
    min-height: 148px;
    margin-top: -37px;
    .dataLabFlowers-grey {
      transition: 0.8s ease-in-out all;
      position: absolute;
      top: 0;
      right: 0;
      left: 0;
      bottom: 0;
      background: rgba(255, 255, 255, 0);
      backdrop-filter: grayscale(1);
      z-index: 99;
    }
    .dataLabFlowers-containerImg {
      min-height: 140px;
      min-width: 80px;
      position: relative;
      img {
        position: absolute;
        bottom: 0;
      }
    }
  }
  .dataLab-dataEntry {
    background: $grey;
    border-radius: 11px;
    height: 8px;
    position: relative;
    overflow: hidden;
    .dataLab-dataEntry-fill {
      transition: 0.8s ease-in-out all;
      background: #CDD52C;
      position: absolute;
      right: 0;
      bottom: 0;
      top:0;
      right: 0;
      left: 0;
      height: 100%;
    }
  }
</style>

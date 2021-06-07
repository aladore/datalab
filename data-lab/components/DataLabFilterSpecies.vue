<template>
  <section class="container">
    <div class="row mt-5">
      <div class="col-md-12">
        <div class="DataLab-panelFilter mt-5 bg-secondary d-flex justify-content-center flex-column">
          <div role="button" @click="showFilter = !showFilter" class="DataLab-panelFilter-button d-flex justify-content-center p-4">
            <div class="text-white text-lg font-weight-bold">Choisir ma culture à comparer</div>
          </div>
          <collapse v-model="showFilter">
            <div class="p-5">
              <div class="text-lg my-3">Sélectionner la culture à afficher :</div>
              <div class="border-top border-dark d-flex justify-content-between py-3">
                <div v-for="(season, name, index) in optionSpecies">
                  <div class="text-white font-weight-bold text-lg mb-3">
                    {{name}}
                  </div>
                  <span v-for="(specie, index) in season.slice(0, 5)">
                    <label class="check">
                      <input type="radio" @click="$emit('set-specie-picked', specie)" :id="specie.id" :value="specie.id" v-model="selectedSpecies.id"/>
                      <div class="box"></div>
                      <span class="checkox-label text-white">{{specie.label}}</span>
                    </label>
                  </span>
                  <collapse v-model="moreFarmingWinter">
                    <span v-for="(specie, index) in season.slice(5, season.length)">
                    <label class="check">
                      <input type="radio" @click="$emit('set-specie-picked', specie)" :id="specie.id" :value="specie.id" v-model="selectedSpecies.id"/>
                      <div class="box"></div>
                      <span class="checkox-label text-white">{{specie.label}}</span>
                    </label>
                  </span>
                  </collapse>
                  <div v-if="season.length > 5" class="text-primary c-pointer" @click="moreFarmingWinter = !moreFarmingWinter">
                    <u>Voir plus de cultures</u>
                  </div>
                </div>
              </div>
            </div>
          </collapse>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
    import { Collapse } from 'uiv'

    export default {
        name: "DataLabFilterSpecies",
        components: {
            Collapse,
        },
        props: {
          argsHref : Array,
          farming : Object,
          optionSpecies: Object,
          selectedSpecies: Object
        },
        data() {
            return {
                moreFarmingWinter: false,
                showFilter : false,
                pickedSpecie: '4'
            }
        },
        mounted() {
        },
        computed : {

        },
    }
</script>

<style lang="scss" scoped>
  @import "../style/theme";
  .DataLab-panelFilter {
    border-radius: 30px;
    overflow: hidden;
    .DataLab-panelFilter-button {
      width: 100%;
      height: 100%;
      cursor: pointer;
      transition: 0.3s ease-in background-color;
      position: relative;
      &:hover {
        background-color: darken($secondary-color, 8%) !important;
      }
      &:after {
        content: '';
        position: absolute;
        width: 75%;
        height: 1px;
        left: 0;
        right: 0;
        bottom: -8px;
        margin: auto;
        background: #fff;
      }
    }
  }
</style>

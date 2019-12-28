<template>
  <div id="app">
    <b-container class="container">
      <b-row class="row">
        <b-col class="column" sm="8">
          <MapPane
            :center="center"
            :zoom="zoom"
            :geojsons="geojsons"
            @onGeojsonsEditted="addGeojson"
          />
        </b-col>
        <b-col class="column" sm="4">
          <p>AAAAAAAAAAAAA</p>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import MapPane from './components/MapPane.vue'

export default {
  name: 'app',
  components: {
    MapPane
  },
  data(){
    return {
      center: [44.176193, 142.39177],
      zoom:14,
      geojsons:[]
    }
  },
  methods:{
    addGeojson:function(geojsons){
      this.geojsons = geojsons
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>

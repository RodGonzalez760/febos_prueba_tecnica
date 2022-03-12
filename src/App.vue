<template>
  <div id="app">    
    <img alt="febos logo" src="./assets/logo_febos.png">
    <FebosComponent 
      :marketSummaries="marketSummaries"
    />
  </div>
</template>

<script>
import FebosComponent from './components/FebosComponent.vue'
import { devServer } from '../vue.config.js'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    FebosComponent
  },
  data () {
    return {
      marketSummaries: null
    }
  },
  mounted () {
    this.getmarkets()
  },
  methods:{    
    async getmarkets() {
      console.log( devServer.port )
      let url = `http://localhost:${devServer.port}/api/v1.1/public/getmarketsummaries`
      console.log(url)
      await axios.get( url )
      .then( response => 
        this.marketSummaries = response.data.result
      ).catch(error => {
        console.log(error)
      })
    },

  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

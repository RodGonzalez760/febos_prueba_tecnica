<template>
  <div class="table-responsive mtop">	
    <div>
      <label class="mleft--label mbottom--label">Ingrese la cantidad de datos a ver por página</label>
      <input type="number" @input="totalPages()" v-model="data_per_page" min="1" max=data_per_page.length>
    </div>
    <table class="table table-striped table-hover">
      <thead>
        <tr>
          <th class="table-secondary" scope="col">Market Name</th>
          <th class="table-secondary" scope="col">High</th>
          <th class="table-secondary" scope="col">Low</th>
          <th class="table-secondary" scope="col">Volume</th>
          <th class="table-secondary" scope="col">Last</th>
          <th class="table-secondary" scope="col">Base Volume</th>
          <th class="table-secondary" scope="col">TimeStamp</th>
          <th class="table-secondary" scope="col">Bid</th>
          <th class="table-secondary" scope="col">Ask</th>
          <th class="table-secondary" scope="col">OpenBuyOrders</th>
          <th class="table-secondary" scope="col">OpenSellOrders</th>
          <th class="table-secondary" scope="col">PrevDay</th>
          <th class="table-secondary" scope="col">Created</th>
        </tr>
      </thead>
      <tbody>
        <tr @dblclick="takeOne(marketSummarie.MarketName)" v-for="marketSummarie in dataPage" :key="marketSummarie.MarketName">
          <th scope="row">{{marketSummarie.MarketName}}</th>
          <td>{{marketSummarie.High}}</td>
          <td>{{marketSummarie.Low}}</td>
          <td>{{marketSummarie.Volume}}</td>
          <td>{{marketSummarie.Last}}</td>
          <td>{{marketSummarie.BaseVolume}}</td>
          <td>{{marketSummarie.TimeStamp}}</td>
          <td>{{marketSummarie.Bid}}</td>
          <td>{{marketSummarie.Ask}}</td>
          <td>{{marketSummarie.OpenBuyOrders}}</td>
          <td>{{marketSummarie.OpenSellOrders}}</td>
          <td>{{marketSummarie.PrevDay}}</td>
          <td>{{marketSummarie.Created}}</td>
        </tr>
      </tbody>
    </table>

    <nav v-if="pageInicial == 1 && pageTope == 10" aria-label="Page navigation example">
      <ul class="pagination justify-content-center">
        <li class="page-item" v-on:click="getPreviusPage()"><a class="page-link" href="#">Anterior</a></li>
        <li v-for="page in pageTope" v-bind:key="page" v-bind:class="isActive(page)" v-on:click="getDataPage(page)" class="page-item">
          <a class="page-link" href="#">{{ page }}</a>
        </li>
        <li class="page-item" v-on:click="getNextPageInt()"><a class="page-link" href="#">...</a></li>
        <li class="page-item" v-on:click="getNextPage()"><a class="page-link" href="#">Siguiente</a></li>
      </ul>
    </nav>
    <nav v-else-if="pageIntermediate.length == 10 " aria-label="Page navigation example">
      <ul class="pagination justify-content-center">
        <li class="page-item" v-on:click="getPreviusPage()"><a class="page-link" href="#">Anterior</a></li>
        <li class="page-item" v-on:click="getPreviusPageInt()"><a class="page-link" href="#">...</a></li>
        <li v-for="page in pageIntermediate" v-bind:key="page" v-bind:class="isActive(page)" v-on:click="getDataPage(page)" class="page-item">
          <a class="page-link" href="#">{{ page }}</a>
        </li>
        <li class="page-item" v-on:click="getNextPageInt()"><a class="page-link" href="#">...</a></li>
        <li class="page-item" v-on:click="getNextPage()"><a class="page-link" href="#">Siguiente</a></li>
      </ul>
    </nav>
    <nav v-else aria-label="Page navigation example">
      <ul class="pagination justify-content-center">
        <li class="page-item" v-on:click="getPreviusPage()"><a class="page-link" href="#">Anterior</a></li>
        <li class="page-item" v-on:click="getPreviusPageInt()"><a class="page-link" href="#">...</a></li>
        <li v-for="page in pageIntermediate" v-bind:key="page" v-bind:class="isActive(page)" v-on:click="getDataPage(page)" class="page-item">
          <a class="page-link" href="#">{{ page }}</a>
        </li>
        <li class="page-item" v-on:click="getNextPage()"><a class="page-link" href="#">Siguiente</a></li>
      </ul>
    </nav>


    <!-- Modal -->
    <div v-if="openModal">
      <transition name="model">
        <div class="modal-mask">
          <div class="modal-wrapper">
              <div class="modal-dialog">
                <div class="modal-content">
                  <div class="modal-header">
                    <h5 class="modal-title" id="staticBackdropLabel"><strong>{{modalMarketName}}</strong></h5>
                    <button type="button" class="btn-close" @click="openModal=false" aria-label="Close"></button>
                  </div>
                  <div class="modal-body">
                    <table class="table table-striped table-hover">
                      <thead>
                        <tr>
                          <th class="table-secondary" scope="col">Bid</th>
                          <th class="table-secondary" scope="col">Ask</th>
                          <th class="table-secondary" scope="col">Last</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr>
                          <td>{{modalData.Bid}}</td>
                          <td>{{modalData.Ask}}</td>
                          <td>{{modalData.Last}}</td>
                        </tr>
                      </tbody>
                    </table>             
                  </div>
                  <div class="modal-footer">
                    <button type="button" @click="openModal=false" class="btn btn-primary">OK</button>
                  </div>
                </div>
              </div>

          </div>
        </div>
      </transition>
    </div>

  </div>  
</template>

<script>
import { devServer } from '../../vue.config.js'
import axios from 'axios'
export default {
  name: 'FebosComponent',  
  props: {
    marketSummaries: Array
  },
  data () {
    return {
      data_per_page: 10,
      nPages: 0,
      dataPage: [],
      pageActual: 1,
      pageInicial: 1,
      pageTope: 10,
      pageIntermediate: [],
      pageFinal: 0,
      sw_totalPages: false,
      openModal: false,
      modalData: {},
      modalMarketName: '',
    }
  },
  methods: {
    totalPages(){      
      this.nPages = Math.ceil(this.marketSummaries.length / this.data_per_page)

      if(this.nPages > 10){
        this.pageFinal = this.nPages
        this.pageInicial = 1
        this.pageTope = 10
      }      
      this.sw_totalPages == false ? this.getDataPage(1) : this.getDataPage(this.pageActual)
    },
    getDataPage(nPage){      
      this.pageActual = nPage
      let ini = 0, fin = 0
      if(this.pageFinal < nPage){
        this.pageActual = 1
        ini = 1
        fin = this.marketSummaries.length
      }else{
        ini = (nPage * this.data_per_page) - this.data_per_page
        fin = (nPage * this.data_per_page)
      }
      this.dataPage = []      

      this.dataPage = this.marketSummaries.slice(ini, fin)
      console.log("Cantidad de registros obtenidos: ", this.dataPage.length )
      console.log("Registros obtenidos: ", this.dataPage )
      this.sw_totalPages = true
    },

    getPreviusPage(){
      if(this.pageActual === this.pageInicial){
        this.getPreviusPageInt()
      }else{
        if(this.pageActual > 1){
          this.pageActual--
        }
      }

      this.getDataPage(this.pageActual)
      this.sw_totalPages = true
    },

    getPreviusPageInt(){
      let pageIntermediateOld = this.pageIntermediate
      this.pageIntermediate = []

      if(this.pageInicial !== 1){
        this.pageActual = this.pageInicial - 1
        this.pageTope = this.pageActual

        this.pageActual === 10 ? this.pageInicial = 1 : this.pageInicial = this.pageInicial - 10
      }else{
        this.pageActual = 1
        this.pageInicial = 1
        this.pageTope = 10
      }

      if(this.pageInicial >= 1){

        if(this.pageActual === 1){
          this.pageIntermediate = pageIntermediateOld
        }else{
          for (let index = this.pageInicial; index <= this.pageTope; index++) {
            this.pageIntermediate.push(index)        
          }
        }

        this.getDataPage(this.pageActual)      
        this.sw_totalPages = true
      }else{
        this.pageIntermediate = pageIntermediateOld
      }
    },
    
    getNextPage(){     
      if(this.pageActual === this.pageTope){
        this.getNextPageInt()
      }else{
        if(this.pageActual < this.nPages){
          this.pageActual++
        }
      }
      this.getDataPage(this.pageActual)      
      this.sw_totalPages = true
    },

    getNextPageInt(){   
      let pageIntermediateOld = this.pageIntermediate
      this.pageIntermediate = []
      this.pageInicial = this.pageTope + 1

      this.pageTope === this.pageFinal ? this.pageTope = this.pageFinal : this.pageTope = this.pageTope + 10

      if(this.pageTope <= this.pageFinal){
        for (let index = this.pageInicial; index <= this.pageTope; index++) {
          this.pageIntermediate.push(index)        
        }

        if(this.pageIntermediate.length > 0){ this.pageActual = this.pageIntermediate[0] }
        
        this.getDataPage(this.pageActual)      
        this.sw_totalPages = true
      }else{
        if(this.pageInicial <= this.pageFinal){
          for (let index = this.pageInicial; index <= this.pageFinal; index++) {
            this.pageIntermediate.push(index)        
          }
          
          if(this.pageIntermediate.length > 0){ this.pageActual = this.pageIntermediate[0] } 

          this.getDataPage(this.pageActual)
          this.sw_totalPages = true
        }else{
          this.pageIntermediate = pageIntermediateOld
        }
      }
    },

    isActive(nPage){
      return nPage == this.pageActual ? 'active' : ''
    },

    async takeOne(marketName){

      this.modalData = []
      this.modalMarketName = ''
      this.openModal = true
      let url = `http://localhost:${devServer.port}/api/v1.1/public/getticker?market=${marketName}`
      console.log(url)
      await axios.get(url)
        .then( response => (        
            this.modalData = response.data.result,
            this.modalMarketName = marketName
        ))
        .catch(error => {
          console.log(error)
        })
    }
  },
  watch:{
    'marketSummaries.length'(){
        this.totalPages()
    }
  }  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
  .mtop {
    margin-top: 50px;
  }
  .mbottom--label {
    margin-bottom: 20px;
  }
  .mleft--label {
    margin-right: 10px;
  }
  .modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, .5);
    display: table;
    transition: opacity .3s ease;
  }

  .modal-wrapper {
    display: table-cell;
    vertical-align: middle;
  }
</style>

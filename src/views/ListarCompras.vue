<template>
  <v-container fluid>
    <v-row class="fixed-bar">
      <v-toolbar :collapse="!expand" color="#f1bc19">
        <v-btn icon @click="expand = !expand">
          <v-icon color="#f3f3f3">mdi-magnify</v-icon>
        </v-btn>
        <v-text-field 
          v-model="searchRifa"
          dark
          v-if="expand"
          dense
          outlined
          hide-details
          label="Pesquisar rifa">
        </v-text-field>
        <v-btn v-if="expand" to="/inicio" class="ml-4"  >
          <v-icon class="mr-2">mdi-cards-variant</v-icon>
          Rifas 
        </v-btn>

      </v-toolbar>
    </v-row>
    
    <v-row v-if="isLoading" justify="start" class="rifas-row">
      <v-col xl="3" lg="4" md="6" sm="6" 
      v-for="i in 6" :key="i">
        <v-skeleton-loader
          type="card-heading"
          class="mx-auto"
        >
        </v-skeleton-loader>
      </v-col>
    </v-row>

    <v-row v-else justify="start" class="rifas-row">
      <v-col xl="3" lg="4" md="6" sm="6"
      v-for="(ticket, index) in searchedTickets" :key="index">
        <v-card elevation="4">
          <v-card-title>
            Rifa: {{ticket.rafle}}
          </v-card-title>

          <v-card-title>
            Número: {{ticket.number}}
          </v-card-title>
        </v-card>
      </v-col>
    </v-row>
    
  </v-container>
</template>

<script>
import api from "../plugins/axios";

export default {
  data() {
    return {
      isLoading: true,
      expand: true,
      tickets: [],
      searchTicket: ''
    }
  }, 
  methods: {
    getTickets() {
      api.get('/tickets', {
        headers: {
          "access-token": localStorage.getItem("token"),
          "client": localStorage.getItem("client"),
          "uid": localStorage.getItem("uid"),
        }
      })
      .then((res) => {
        this.tickets = res.data;
        this.isLoading = false;
      })
      .catch((err) => {
        console.log(err);
      })
    },
    reduceDescription(desc) {
      return desc.slice(0, 50);
    },
  },
  computed : {
    searchedTickets : function(){
      var self = this;
      if( this.searchTicket == ''){
        return this.tickets;
      }
      return this.tickets.filter(function(item) {
        return item.rafle.toLowerCase().indexOf(self.searchTicket.toLowerCase()) >= 0;
      });
    }
  },
  mounted() {
    this.getTickets();
  }
}
</script>

<style>
  .container {
    padding-top: 0 !important;
  }

  .fixed-bar {
    position: fixed;
  }

  .loading-row {
    height: 100vh;
  }

  .upload-box {
    max-width: 100px !important;
    min-height: 100px !important;
    border: 1px solid;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 10px;
  }

  .input-box {
    display: flex;
    justify-content: center;
  }

  .input-box label {
    width: 100px;
  }

  .rifas-row { 
    margin-top: 100px;
  }

  .rifa-preco {
    height: 36px;
    min-width: 64px;
    padding: 0 16px;
    color: #ffffff;
    background-color: #4caf50 !important;
    border-color: #4caf50 !important;
    font-size: .875rem;
    box-shadow: 0 3px 1px -2px rgba(0,0,0,.2), 0 2px 2px 0 rgba(0,0,0,.14), 0 1px 5px 0 rgba(0,0,0,.12);
    align-items: center;
    border-radius: 4px;
    display: inline-flex;
    flex: 0 0 auto;
    font-weight: 500;
    letter-spacing: .0892857143em;
    justify-content: center;
    outline: 0;
    position: relative;
    text-decoration: none;
    text-indent: .0892857143em;
    text-transform: uppercase;
    transition-duration: .28s;
    transition-property: box-shadow,transform,opacity;
    transition-timing-function: cubic-bezier(.4,0,.2,1);
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    vertical-align: middle;
    white-space: nowrap;

    position: absolute;
    left: 0;
    top: -10px;
    z-index: 1;
  }

  .pontos {
    padding: 16px;
    font-size: 15px;
  }

  .green-text{
    color: #4caf50;
  }

  .warning-text {
    color: #fb8c00;
  }

  .warning-text, .green-text {
    font-size: 20px;
    font-weight: 600;
  }

  .upload-image-preview {
    width: 50%;
    border-radius: 25px;
  }
</style>
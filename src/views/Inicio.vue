<template>
  <v-container fluid>
    <v-row justify="center">
      <v-dialog fullscreen v-model="formModal" persistent hide-overlay transition="dialog-bottom-transition">
        <v-toolbar dark color="#f1bc19">
          
          <v-toolbar-title class="text-center">Criar rifa</v-toolbar-title>

          <v-spacer></v-spacer>

          <v-toolbar-items>
            <v-btn icon dark @click="formModal = false">
              <v-icon>mdi-close</v-icon>
            </v-btn>
          </v-toolbar-items>
        </v-toolbar>

        <v-card>
          <v-card-text>
            <v-container>
              <v-row justify="center">
                <v-col cols="10">
                  <v-text-field 
                    outlined required v-model="rafle.name"
                    label="Titulo da rifa"
                  >
                  </v-text-field>
                </v-col>
                
                <v-col cols="10">
                  <v-text-field 
                    outlined required v-model="rafle.description"
                    label="Descrição da rifa"
                    >
                  </v-text-field>
                </v-col>

                <v-col cols="10">
                  <v-text-field type="number"
                    outlined required v-model="rafle.price"
                    label="Preço do ponto (R$)"
                    >
                  </v-text-field>
                </v-col>

                <v-col cols="10">
                  <v-text-field 
                    type="number"
                    outlined required v-model="rafle.numbers_quantity"
                    label="Quantidade de pontos">
                  </v-text-field>
                </v-col>

                <v-col cols="10">
                  <v-text-field 
                    outlined required v-model="rafle.premium"
                    label="Descrição do prêmio">
                  </v-text-field>
                </v-col>

                <v-col cols="10">
                  <v-text-field 
                    persistent-hint hint="Ex: 20/10/2020 16:00"
                    outlined required v-model="rafle.date_time"
                    label="Data do sorteio">
                  </v-text-field>
                </v-col>

                <v-col cols="10">
                  <h2 class="title text-center mb-4">Imagem do prêmio</h2>
                  <v-btn-toggle v-model="image_upload_mode" rounded>
                    <v-btn>
                      <v-icon class="mr-4">mdi-link-variant</v-icon>
                      Baixar de um link
                    </v-btn>
                    <v-btn>
                      <v-icon class="mr-4">mdi-file-upload</v-icon>
                      Enviar imagem
                    </v-btn>
                  </v-btn-toggle>

                  <v-text-field class="mt-10"
                    v-if="image_upload_mode == 0"
                    outlined required v-model="rafle.image_url"
                    label="Link da imagem">
                  </v-text-field>

                  <div v-else class="input-box">
                    <label for="upload">
                      <div class="upload-box mt-10">
                        <v-icon>mdi-file-upload</v-icon>
                      </div>
                      <input @change="uploadImage()" type="file" id="upload" style="display:none">
                    </label>
                  </div>

                  <img class="upload-image-preview mt-10" :src="rafle.image_url" />
                </v-col>
                
              </v-row>
            </v-container>
          </v-card-text>

          <v-row justify="center">
            <v-col cols="6">
              <v-btn block color="success" @click="createRaffle()" :disabled="isLoading">
                <v-progress-circular v-if="isLoading"
                  indeterminate
                  color="white"
                ></v-progress-circular>
                Salvar
              </v-btn>
            </v-col>
          </v-row>
        </v-card>
      </v-dialog>
    </v-row>

    <v-dialog v-model="denounceModal" width="600">
      <v-card>
        <v-row>
          <v-col lg="3" md="2" sm="2" cols="2"></v-col>
          <v-col lg="6" md="8" sm="8" cols="8">
            <h2 class="text-center title">Denunciar essa rifa</h2>
          </v-col>
          <v-col lg="3" md="2" sm="2" cols="2">
            <v-btn icon @click="denounceModal = false">
              <v-icon>mdi-close</v-icon>
            </v-btn>
          </v-col>
        </v-row>

        <v-divider></v-divider><br>

        <v-card-text> 
          Ajude-nos a entender o que está acontecendo com essa rifa. Como você a descreveria?
        </v-card-text>

        <v-row no-gutters>
          <v-col cols="11">
            <v-chip-group class="ml-4" column active-class="primary--text">
              <v-chip>Enganoso ou fraudulento</v-chip>
              <v-chip>Sexualmente inadequado</v-chip>
              <v-chip>Ofensivo</v-chip>
              <v-chip>Violência</v-chip>
              <v-chip>Conteúdo proibido</v-chip>
              <v-chip>Já vi essa mesma rifa várias vezes!</v-chip>
              <v-chip>Outro</v-chip>
            </v-chip-group>
          </v-col>
        </v-row>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="info"
            @click="denounceModal = false">
            Enviar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="buyRafleModal" width="600">
      <v-card>
        <v-row>
          <v-col lg="3" md="2" sm="2" cols="2"></v-col>
          <v-col lg="6" md="8" sm="8" cols="8">
            <h2 class="text-center title">Comprar pontos</h2>
          </v-col>
          <v-col lg="3" md="2" sm="2" cols="2">
            <v-btn icon @click="buyRafleModal = false">
              <v-icon>mdi-close</v-icon>
            </v-btn>
          </v-col>
        </v-row>

        <v-divider></v-divider><br>


        <v-row no-gutters justify="center">
          <v-col cols="10">
            <h3 class="title">
              Você deseja participar do sorteio da rifa {{selectedRafle.name}}?
            </h3>

            <div class="mt-4">
              Um ponto custa R$ {{selectedRafle.price}}
            </div>

            <v-text-field type="number" class="mt-8"
              outlined required v-model="ticket.number"
              label="Qual número da rifa você quer?"
            >
            </v-text-field>
            
          </v-col>

          <v-row justify="center">
            <v-col cols="6">
              <v-btn block color="success" @click="buyRafle()" :disabled="buyingRafle">
                <v-progress-circular v-if="buyingRafle"
                  indeterminate
                  color="white"
                ></v-progress-circular>
                Comprar!
              </v-btn>
            </v-col>
          </v-row>
        </v-row>

        <v-divider></v-divider>

      </v-card>
    </v-dialog>

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

        <v-btn icon @click="formModal = !formModal" v-if="!expand">
          <v-icon color="#f3f3f3">mdi-plus-circle-outline</v-icon>
        </v-btn>

        <v-btn @click="formModal = !formModal" class="ml-4"  >
          <v-icon class="mr-2">mdi-plus-circle-outline</v-icon>
          Criar rifa
        </v-btn>
        
        <v-btn to="/minhas-compras" class="ml-4"  >
          <v-icon class="mr-2">mdi-format-list-numbered</v-icon>
          Minhas compras 
        </v-btn>

      </v-toolbar>
    </v-row>
    
    <v-row v-if="isLoading" justify="start" class="rifas-row">
      <v-col xl="3" lg="4" md="6" sm="6" 
      v-for="i in 6" :key="i">
        <v-skeleton-loader
          type="card"
          class="mx-auto"
        >
        </v-skeleton-loader>
      </v-col>
    </v-row>

    <v-row v-else justify="start" class="rifas-row">
      <v-col xl="3" lg="4" md="6" sm="6"
      v-for="(rifa, index) in searchedRifas" :key="index">
        <v-card class="rifa-card" elevation="4">
          <button class="rifa-preco">
            R$ {{rifa.price}}
          </button>

          <v-img cover
            :src="rifa.image_url"
            height="200px">
          </v-img>

          <v-card-title>
            {{rifa.name}}
          </v-card-title>

          <v-card-subtitle class="text-start">
            {{reduceDescription(rifa.description)}}...<br>
            <span class="mt-2" style="font-weight: bold">Criada por:</span> {{rifa.owner}}

          </v-card-subtitle>

          <v-card-actions>
            <v-btn color="green" text @click="openBuyModal(rifa)">Comprar ponto</v-btn>
            <v-spacer></v-spacer>
            <v-btn icon @click="rifa.show = !rifa.show">
              <v-icon>{{ rifa.show ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
            </v-btn>
          </v-card-actions>

          <v-expand-transition>
            <div v-show="rifa.show">
              <v-divider></v-divider>

              <v-card-text class="text-start">
                {{rifa.description}}
              </v-card-text>

              <v-card-text class="text-start">
                Data e hora do sorteio: {{getDataTime(rifa.date_time)}} 
              </v-card-text>

              <div class="text-start pontos">
                <span class="warning-text">{{rifa.total_tickets}}</span> pontos reservados de <span class="green-text">{{rifa.numbers_quantity}}</span> pontos!
              </div>

              <v-card-actions>
                <v-btn color="error" text @click="openDenounceModal()">Denunciar!</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </div>
          </v-expand-transition>
        </v-card>
      </v-col>
    </v-row>

    <v-snackbar v-model="snackbar" light bottom right>
      Ponto comprado com sucesso!
      <template v-slot:action="{ attrs }">
        <v-btn color="success" text v-bind="attrs" @click="snackbar = false">
          ok
        </v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<script>
import api from "../plugins/axios";

export default {
  data() {
    return {
      isLoading: true,
      buyingRafle: false,
      expand: true,
      formModal: false,
      denounceModal: false,
      buyRafleModal: false,
      snackbar: false,

      selectedRafle: {},
      image_upload_mode: 0,
      images: [],
      rifas: [],

      rafle: {
        name: "",
        description: "",
        numbers_quantity: "",
        premium: "",
        price: 1,
        date_time: "",
        image_url: "",
      },
      ticket: {
        number: 1,
        rafle_id: 0
      },
      searchRifa: ''
    }
  }, 
  methods: {
    getRifas() {
      api.get('/rafles?filter=future=true', {
        headers: {
          "access-token": localStorage.getItem("token"),
          "client": localStorage.getItem("client"),
          "uid": localStorage.getItem("uid"),
        }
      })
      .then((res) => {
        for (var i in res.data){
          res.data[i].show = false;
        }
        this.rifas = res.data;
        this.isLoading = false;
      })
      .catch((err) => {
        console.log(err);
      })
    },
    createRaffle() {
      this.rafle.price = parseInt(this.rafle.price);
      this.isLoading = true;
      api.post('/rafles', this.rafle, {
        headers: {
          "access-token": localStorage.getItem("token"),
          "client": localStorage.getItem("client"),
          "uid": localStorage.getItem("uid"),
        }
      })
      .then(() => {
        this.isLoading = false;
        this.formModal = false;
        this.getRifas();
      })
      .catch((err) => {
        console.log(err);
      })
    },
    buyRafle() {
      this.buyingRafle = true;
      this.ticket.number = parseInt(this.ticket.number);
      this.ticket.rafle_id = this.selectedRafle.id;
      this.isLoading = true;

      api.post('/tickets', this.ticket, {
        headers: {
          "access-token": localStorage.getItem("token"),
          "client": localStorage.getItem("client"),
          "uid": localStorage.getItem("uid"),
        }
      })
      .then(() => {
        this.buyingRafle = false;
        this.buyRafleModal = false;
        this.snackbar = true;

        this.getRifas();

        setTimeout(() => {
          this.snackbar = false;
        }, 2500)
      })
      .catch((err) => {
        console.log(err);
      })
    },
    reduceDescription(desc) {
      return desc.slice(0, 50);
    },
    openDenounceModal() {
      this.denounceModal = true;
    },
    openBuyModal(rafle) {
      this.buyRafleModal = true;
      this.selectedRafle = rafle;
    },
    uploadImage() {
      var file   = document.querySelector('input[type=file]')['files'][0];
      let reader = new FileReader();
      if(file) {
          reader.onload = () => {
          var imgBase64 = reader.result;
          this.rafle.image_url = imgBase64;
        }
        reader.readAsDataURL(file);
      }
    },
    getDataTime(dt){
      var dataTimeObj = dt.split('T');
      var data = dataTimeObj[0].split('-');
      data = `${data[2]}/${data[1]}/${data[0]}`;

      var hourObj = dataTimeObj[1].split('.');
      hourObj = hourObj[0].split(':');
      hourObj = `${hourObj[0]}h${hourObj[1]}`;
      return `${data} às ${hourObj}`;
    }
  },
  computed : {
    searchedRifas : function(){
      var self = this;
      if( this.searchRifa == ''){
        return this.rifas;
      }
      return this.rifas.filter(function(item) {
        return item.name.toLowerCase().indexOf(self.searchRifa.toLowerCase()) >= 0;
      });
    }
  },
  mounted() {
    this.getRifas();
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
<template>
  <v-container fluid>
    <v-row justify="center">
      <v-dialog v-model="formModal" persistent max-width="600px">
        
        <v-card>
          <v-card-title>
            <span class="headline">Criar rifa</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row justify="center">
                <v-col cols="10">
                  <v-text-field 
                    outlined
                    required
                    label="blabla"
                    >
                  </v-text-field>
                </v-col>
                
                <v-col cols="10">
                  <v-text-field 
                    outlined
                    required
                    label="blabla"
                    >
                  </v-text-field>
                </v-col>

                <v-col cols="10">
                  <v-text-field 
                    outlined
                    required
                    label="blabla"
                    >
                  </v-text-field>
                </v-col>
                
              </v-row>
            </v-container>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="formModal = false">Cancelar</v-btn>
            <v-btn color="blue darken-1" text @click="formModal = false">Salvar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-row>

    <v-row class="fixed-bar">
      <v-toolbar :collapse="!expand" >
        <v-btn icon @click="expand = !expand">
          <v-icon>mdi-magnify</v-icon>
        </v-btn>

        <v-text-field 
          v-if="expand"
          dense
          outlined
          hide-details
          label="Pesquisar rifa">
        </v-text-field>

        <v-btn icon @click="formModal = !formModal"  v-if="!expand">
          <v-icon>mdi-plus-circle-outline</v-icon>
        </v-btn>

        <v-btn class="ml-4" v-else>
          <v-icon class="mr-2">mdi-plus-circle-outline</v-icon>
          Criar rifa
        </v-btn>


      </v-toolbar>
    </v-row>

    <v-row style="height: 2000px" >

    </v-row>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      expand: false,
      formModal: false,
    }
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
</style>
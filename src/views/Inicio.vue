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
                    outlined
                    required
                    label="Titulo da rifa"
                    >
                  </v-text-field>
                </v-col>
                
                <v-col cols="10">
                  <v-text-field 
                    outlined
                    required
                    label="Descrição da rifa"
                    >
                  </v-text-field>
                </v-col>

                <v-col cols="10">
                  <v-text-field 
                    outlined
                    required
                    label="Preço do ponto (R$)"
                    >
                  </v-text-field>
                </v-col>

                <v-col cols="10">
                  <v-text-field 
                    type="number"
                    outlined
                    required
                    label="Quantidade de pontos">
                  </v-text-field>
                </v-col>

                <v-col cols="12">
                  <upload-image is="upload-image"
                    url=""
                    :disable_upload="true"
                    :max_files="2"
                    v-model="images"
                    button_html="<div class='upload-box'><i aria-hidden='true' class='upload-icon v-icon notranslate mdi mdi-plus-circle-outline theme--light' style='color: #458fb9; caret-color: #458fb9;'></i></div>'"
                    button_class="upload-btn"
                    :resize_enabled="true"
                    :resize_max_width="640"
                    v-on:upload-image-loaded="uploadImageLoaded"
                  ></upload-image>
                </v-col>
                
              </v-row>
            </v-container>
          </v-card-text>

          <v-row justify="center">
            <v-col cols="6">
              <v-btn block color="success" @click="formModal = false">
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


      </v-toolbar>
    </v-row>
    
    <v-row v-if="isLoading" justify="start" class="rifas-row">
      <!-- <v-col cols="4">
        <v-progress-circular
          :size="100"
          color="#f1bc19"
          indeterminate
        ></v-progress-circular>
      </v-col> -->
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
            :src="rifa.image"
            height="200px">
          </v-img>

          <v-card-title>
            {{rifa.title}}
          </v-card-title>

          <v-card-subtitle class="text-start">
            {{reduceDescription(rifa.description)}}...
          </v-card-subtitle>

          <v-card-actions>
            <v-btn color="green" text>Comprar ponto</v-btn>
            <v-spacer></v-spacer>
            <v-btn
              icon
              @click="rifa.show = !rifa.show">
              <v-icon>{{ rifa.show ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
            </v-btn>
          </v-card-actions>

          <v-expand-transition>
            <div v-show="rifa.show">
              <v-divider></v-divider>

              <v-card-text class="text-start">
                {{rifa.description}}
              </v-card-text>

              <div class="text-start pontos">
                <span class="green-text">{{rifa.reserved}}</span> pontos reservados de <span class="warning-text">{{rifa.point_quantity}}</span> pontos!
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
  </v-container>
</template>

<script>
import UploadImage from 'vue-upload-image';

export default {
  components: {
    UploadImage 
  },
  data() {
    return {
      isLoading: true,
      expand: false,
      formModal: false,
      denounceModal: false,

      images: [],
      rifas: [
        {
          title: 'Moto 0km',
          description: 'Precisamos arrecadar R$ 20.000 para ajudar no tratamento do pequeno Pedro!',
          price: 10.00,
          point_quantity: 2000,
          reserved: 500,
          show: false,
          image: 'https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcQaz4YjCSy_M_5i1-yACHwREIiciNMd9Z77Mw&usqp=CAU',
        },
        {
          title: 'Carro 2015',
          description: 'Quero quitar a minha casa!',
          price: 50.00,
          point_quantity: 100,
          reserved: 50,
          show: false,
          image: 'https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcQogjQ8lEks0E4ItYkcxqKBZXGtPubDZuTe2Q&usqp=CAU',
        },
        {
          title: '02 Cestas básicas',
          description: 'Preciso comprar 2 caixas de um rémedio caro para a minha mãe.',
          price: 5.00,
          point_quantity: 100,
          reserved: 15,
          show: false,
          image: 'data:image/jpeg;base64,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',
        },

        {
          title: 'Bicicleta',
          description: 'Estou desempregado e tenho contas a pagar ',
          price: 10.00,
          point_quantity: 200,
          reserved: 63,
          show: false,
          image: 'data:image/jpeg;base64,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',
        },
        {
          title: 'TV 32" usada',
          description: 'Minha filha está precisando de fraldas!',
          price: 20.00,
          point_quantity: 150,
          reserved: 3,
          show: false,
          image: 'data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBxMTEhUSEhMVFRUVFxUVFRUVFRUVFRUVFRcXFxUVFRUYHSggGBolHRUVITEhJSkrLi4uFx8zODMtNygtLisBCgoKDg0OFQ8QFysdFR0rKy0tLSsrLSstLS0tKystLS0tLS0tLS0tLS0tKy0rKysrLSstKy0tLS0yKzgtLTgrK//AABEIAMIBAwMBIgACEQEDEQH/xAAcAAAABwEBAAAAAAAAAAAAAAAAAQIDBAUGBwj/xABFEAACAQIBBQ0EBwYGAwAAAAAAAQIDEQQFEiExUQYiQVJhcYGRobHB0fATYnKSFCMyQlOC4RUWM0OisiQ0c8LS8ZOz4v/EABkBAAMBAQEAAAAAAAAAAAAAAAABAgMEBf/EACMRAQEAAQMFAQADAQAAAAAAAAABAgMRExIhMTJRYRQiQQT/2gAMAwEAAhEDEQA/ANNlrI0Kt5U3GM+FXWbLyfKYjGQcJOMlZrWmaGvpIGKwyklfXbWc+eUqsJYpHXEOqiXUyc78HUNPJ75O0z7NUZ1UD2qFvAvk7RDwT5BdgUqi2jkag3TwDY6smy9MAVGoH7UQ8nT2dq8xP7OnsfX+o+xJCqoVnoi/QZbJA+hz2SAJaqIWpoh/R56rSvssxaw0+LLqYBK9ohOdEiSpyWtS6mIhflA0/OiJk4kS7EubAJTmvVyPPGWdrMbVZg9ryANkmNdsO72kSVYQqr5esAnuo+TqYSlfb65yIq72AeIfCPckvMfGA4vjEVYgP2wA/my2h5rGPaio1g7hIito7B+rIjxmhaqCCVGfqyHaciFGqixybh51ZWpxcnyalzvUgkpU9HVrAaGluZqWV3BPZvnbpSAa8dZ9cVNVEaovXUTJcAxKJnlO64iuI3KHrpHqrtd7Cm/eKhwza/LLwQpjv4VbIlulpGqkNK534kb9v4e/8T+mXkFPLdBtP2i1vglw9BXRfg6osKVPQSoQZWU8tUNXtY9LJlPK+H/Gp9MkiLjfg3n1KzAlAQsq0G9Fan88fMX9OpcFWn88RbUbwHAKMPXT/wBC1Xi/5kXzNcP/AGOU5J/eXWuZvqYHAjRfAk12vl7STSpgSTa31m3o5FZtX6kOTTS6dPJrFsDdanZPYRqFPer1wllVjo6PAjYaNorp7XcBDbpDWIoLN1LqJ+aN4hLNBSrWHVlvVr2IFTCRT+zF6U9SJrWhdL7R9wTt0DJTVcDHix6kGsDDiLqLGrHT65Q4wQbBVzwEL/YWrl5CPVyfHi95eTp6ejxX6jc6WldPeipCUccnR2dpI/Zcdj6yyVNL11EqFJFbFuz88lR5QUsk85fzgrCsOlsK6S3VFPIj2iv2C9O+7DQU0KcLdfkHSnqUeDyLFNZzctfItBdUIZtlHepJ6Fo2AUe63aOJaehePkOTunK9mtw32I/DHuQY5TjZJciAdOzm3c7epcyEOQVV8BHnPxOPPy6sRYiN09O3tRyyqzps6hzGrrZrof6nVMtic8sMFhIzp15NtSpwjONrWbdSEGnyWm+orJRe06bGUpeeH7QYcXtDSZCj3tA/aDDiwWfIBpCmGqhH08gqN/TAJUKr2kzD1Jt2UpLpaKeVa2gl5OxW/V9u22jpCSJtqdPH1Y6Paz+eXmFHK1Zaq1T55eZDqyu21qvtQzJvY+teYrJ8OWraOXMQv59T55eYp5fxGr21T5mU6YLi6YrerlbosT+NLs8hyO6fFL+c+qPkUV/VgX9WYdM+Der396MT+K/lh/xFfvTiOOvkh5FBf1pDuHTPg6r9aJbrsRxo/IvAN7r6/ufLzcvIZu/KB+tI+mfC3rTLdfW2Q+V+ZIhu2rL7tN9Ev+RkV0daD0+mg6YW9bD99qn4dP8Aq8x2nu2mv5cOuRjFf00LUXsKmOJdVbuju6a/lR+d+Q9+/N/5K/8AJ/8AJg4xlsZKwsdOkqacqLnY6fkbKsq6TdJwi75ss66k42zrKy1Zy08pd043fy/7iswGGUXhoJWUcJTk/iqylOT7S3wq36XvQXeRlJL2XjbZ3avNDFAK2YuYYrRKS2OXeyJUfrnH8oStWqR5Zd7I8zhz8u3DwZmjm2IW+lzvvOlyRzfGxtUmvekv6ma6F71OqkZKlvMQttDurUSrkybgatlW96k1/XTfgQ2zrvhzm2AUwWIVBIUkJsKQlBmis0cg9o77F862gN9lTUjvmSsLHuducdqYXTzk7J+BbeoqQrki0qLuw5QLyvgGk30FZUpisKVEzQWHZRE2EsiwVhxoIARYTYcaEtACAmxTENiA7guJuAZUtChsVcqVJ6JZZNjq5iqiy6yVHSkbafljqeHXLWxDVvsUMNDm+rT8Sdg1eqvii+7zI2I/zeI0avYx+WklYm5KX1sfi7oxOfL2bY+rTWCFANGbl2Vl9bP4pd5AqTtbluuxtFrlaP10/iIFuQ4M/Ltw8IsXdJ7bdxz3Ka+uqfHP+5nSZLQc7y1oxFVe/LvL0L3Tq+EGi7Z/wNdz8Blj9JrffDLuZHkdbnEAJhEqORm/WkcU1wrqfmMoNCGx+MVwPr0E7B5y4NHDwpldEew+LlGW8bT2pgP8arB5KjWW8+1xOH8u3mNLue3LSclveQqNyeKlOcVKmqjvwK0uuFr9J3LIUF7NO1nqd7X6TWZSRjtcrs5ZumyE6aSttZgcoU1F6T0VunwEKlJtxba4FrfJqZwzdRNU5SjGjGNn99OclyWlvewm3ebn63Zl2r6tIXs9rS9bENfSpSlZvRs4FzLUhxkRuZxE0lwvsI6xL2JDuJpt6iNDDyWsRxJhK6AwU42VgMAQxDFsQwABNgCYypunVux24zGFg2xxNPqoaTc+r1qcdsorrdjL4SDnOMFrlKMVzyaS7y/yPJxxMVpvGp2xl+hthWWcdglWviMY9lZr5FYs8j6ased9xk8hV7wrTTvnV6jT23aszWZCd6q5n3GF9ms9WmAJuA1Yuc5X/jT6H2IgTRZZaj9dLlt/aiBJHnans7cPEMZuo53uhX+JqfE+46JU0M55un/zFTnX9qK0PYanhRYeo/aPmn/bIVcaoP6z5u5i0zsYWDFCM4POEDkVfUORpbWlzvwWkYz2KTFQfSS4W+zvG6WLzZO0Y69clndj0dglMiVJb5he5yNfkzLVR2UpuyehaormitCOubi90ebC0noXeef8NiLNGowOWHCCV9Ldy8bNtqzyx77utbod2a0xVs3Yc2y9lJzu4SzlrcJpTtzX1rm0lBlDKzk9ZVyxEpPQwyym2xTC27l+1i5XzVF34G7dTuSlG/D16CqliLvTr2+ZNw9W6InZtt2PzpvZ65xqw4mE2IG2NSHmNSQA1IQxUxIgATYBLGKDY22KkNsoisLLfp+8n2l/uau8TTtpec302bM9h3vkafcViMzFQqJXzI1JWfJTl5l4s8nQdx8L4ZPhcpN/Mkazcu26rb5TM7k6L+iUrcN7/PJrxNfuejab+F96Mpf7NL6tBcAAGvdg5/lxfXPmT7Eiu8iyy/8AxfyrxK1ajz9X2dmn4hmoc93Wr/ET5VF/0o6FVfcYDdmrV38MfEej7HqeGUhLfeuEKnPQGtoxc692Wxc6o7SmRLjtKQFYlqQpMjKTC9qG57JecQak9PSHKsMpiORIp1CZ9KK2LFKYQ9kyVa47hJaSBnEjCVLMBsdx1Gzzlw6x/Jz1klaRUY21DLcoIAGwSbkxuTHJDUgM3JiQSCEBCWGwhkQxLF5ompGwwbp6zVbjGlOrJ2tHD1np0q7jmrR+YytLWXWR5WjU+FLrkioiuubnVbDUV7vhc1u5+O/l8Pj+hlsjK1GitkI6Py2NVuc1z5kY4X+zXP1XgAAOhzbMDuij9Yn7q75FVB6C33RLfx+HxZUJnBrezr0vWGZmD3ar65fAu+Ru6rMNuzX1sfgX90idH2Xn4ZBMjSZIkR2djKEhphAAy1UEthAEAAAAApMAkNsAW2O0ZaSPcNSAL+jLQh+KIWDqXiiUmNNLCYQmQEEhqQ4xuYGZkFYVIJsQJsBhNiJMZDnMakKYiWoZE0i3yd9mX5SppltkyN9G2SXrrH/grteFhmwpr3bdVzS7nF9vo8Shaso8z59DSL/c99mfOjDSv9mmp6rcAVwHS5tmH3SLTDmfeigUjQbo9dP83+0z9XacWt5dWl6kTfrqMZu1W/g/dfZJmxqT2mQ3a66b2qffHzI0vZefhiai09Iy0P1lp6Rmes7GUJYkVYFgMkAACAAAAAAAAAAAAAC0ya9DXKWESryXLWWVxppbYTZW46u72T1AwmJd7N3QBYMQw2xuUgGxDYhsEpCc4QGxthtiWxgGImxTYmSHCCki9yGrzpLbUj3pFHTRo9zFO9fD/wCpF9Ur+AXwNu7r+fq5Eu1ml3PLeS+LwRlqUtPV67TVZA/hv4n3Iw0vK9XwsgAAdLnYrdJqhzy7kUUo6C+3RfZh8T7ii4Dj1vLp0vCLUiZTdrHRTfxf7fI11XhMtuyh9XTfvPuI0/aNM/VhMQtLG8TG0nzkivHSJqWk27dp2MIiB2HWls7Q81bO0DRmESc1bO0GatnawG6OgWJFlxV1sO64q7QG6KGkSoyXEj2+ZJwmLUJJ+xpTs72nGUk+R77UA3VriCxYVa6en2dNc0f1GnV92HygNycFe+gmOcuFCcDTlUnGEY5zloSjG7b5ET6+AnFtSpyTjfOvFrNto06NGnQBKaqrsk4fDNb59AqorN6F1Ik4ampR1K6AG2IaJPsVsAqXIA3Q3EQ4k/2QPZBsW6A4hZpYeyFKiPYbqzMD9kWfseQHsOQNi6ldCm1rNZuVherhl71RvmSb8yl+itmk3KUbYijf7saj7GvEWfaUY+0dAows9Gxeuw1+Qf4K+KXfbwMlHWlzGuyL/Bj+b+5nPo+Wut4TrgCAdLmY7L32Y/F4MpFHwL7LK3sfiXdIqVDwOXW8unS8IFeBnN19H6mL9/wZq6kdZExmEjUWbJJq617dpnhdrK1s3jlM8O2JWGOmTyBRs3mLQnwLg6Bp5Fo/hx1bF5HTy4spp5ObPCivop02nkSjxI/KvLnH45Do8SPUvInmxHHk5X9FCeFR1uORaXEXUgPJlJfdQc+J8dcjeGQFhUdeeBpr7veJhhI6Fba2+RJaOsXPPh8VcmWT3wXHaGS5t2UJPmi2dbhgE3nJWV3ZbUiTQwsVwIX8ifBxVyOvkOom06dRNa04Sv3DUsjVOCnP5ZeR2qWGjJ3aTb6RMcNDirqJv/R+Hw365JkfB16NSNWnCcZRu03BtaU0001p0Nljip4ypnZznad85ZiV78GrUdSjhYcWPUhieFjduy+z4h/I/D4f1yRZCqv7k+ocp5DqrVCXWvM61QpLNjdLWlqChTV+vvH/ACPwuH9cthkatxH1rzFxyJW/D7vM6vOKG5xF/IvwuH9cvWQq3E7UKWQa3E7zpM4+usalHT0+CK578HF+ufR3PV+Iu3yHY7nK/FXb5G/zdfP4IOSHzUcM+sD+71bZH10Dq3MVnrsbNLfIerXt0Nj58viboxjae5attXZ5lrkjIUqU1VqNaItJc7Wx8jNFTTt2diE4h6NOrh7b9hGWrb2PHSku6Phat99y6L8j/Q22Sl9VDmfezEU4u0dlzb5M/gw+FD0fNLW8JdgCQHQ52Uyut7H4l3Mq3qLrK9LeJ+9HxKeS4Oc59Wb1vpXsYlpYeaLhEWqRn01r1Izhoa5JeBHlHXzE2VPS/WwazNHQHTT6oKlDR0D8Yh06ejsH40ZX1PqYrhR1w20R5xLH6JPiS+V+QI4Go9UJP8rDoo64rHEKENGr0rvyLX9k1fw5dTFQyNV/DfYvHkXaPjvwck+oag2752nTo2X/AOyVCBMwWTakleK0Jyi1da1a99O0mQyPV2L5kHFkOSfVYo6BpRLmOSJ6t7t1iqeQ5tJ3jp5X5BxZfByxWJEevHRLmXrtNB+w5caPaJlueb1zXy/qPgyHNiz8Y6lsDpLQuaxov3f039p1R/UVDc/FW374OBcGgfDSutFE0Nyp6TTLIcONLs8g/wBiU9suteQ5oVPNiy0ojFSLurbdPNpNisiUtkusCyJR4v8AVLzKmjU80ZJ09fP4AcDYLJVJfc7ZeYf7OpcSPVcfCXNGJzNI66d9BsfoNP8ADh8qFrDR4sepFcJczHSQmpSvo5DaZi2LqBmiuiOb8Yt0noST0cncazJ8WqUE9eau4ksIeGHSjLPqAAAGiFTlNfVvnXeUlKKb0pAATkvHws8Nh4WW9j1InQw0OJH5UAA4D0aEeLHqXKOKC2LqAAtBxCwAHCKQ1hOHnAAk6ksJgAEJCyZ9qt/qL/10yxCAPJOHg3P73w+DH46kABEXBhIAAARDAAdAAAAoEhAAAASwACEIQwAGCQBAEBMJBgJMhhgAAf/Z'
        },
        {
          title: 'Fogão novo',
          description: 'Arrecadação de dinheiro para a paroquia do meu bairro!',
          price: 15.00,
          point_quantity: 200,
          reserved: 199,
          show: false,
          image: 'https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcTHRFGgHjtpZ2NEFhElkeUQREKDSugk5qFH3Q&usqp=CAU'
        },
      ],

      searchRifa: ''
    }
  }, 
  methods: {
    getRifas() {
      setTimeout(() => {
        this.isLoading = false;
      }, 1500)
    },
    uploadImageLoaded(image) {
      // image.name || image.file
      this.images.push(image);

      if(this.images.length > 2) {
        this.images.shift();
        this.$delete(this.files, 0);
        this.$delete(this.image, 0);
      }
    },
    reduceDescription(desc) {
      return desc.slice(0, 50);
    },
    openDenounceModal() {
      this.denounceModal = true;
    }
  },
  computed : {
    searchedRifas : function(){
      var self = this;
      if( this.searchRifa == ''){
        return this.rifas;
      }
      return this.rifas.filter(function(item) {
        return item.title.toLowerCase().indexOf(self.searchRifa.toLowerCase()) >= 0;
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
    min-width: 200px !important;
    min-height: 200px !important;
    border: 1px solid;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 10px;
  }

  .upload-btn:focus {
    outline: none;
    border: none;
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
</style>
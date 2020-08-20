<template>
  <div>
    <v-app-bar color="#f1bc19" dark>
      <v-toolbar-title>Rifando</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn to="/" text class="mr-5">
        Cadastrar
      </v-btn>
    </v-app-bar>

    <v-container fluid>
      <v-row justify="center">
        <v-col cols="10">
          <h1 class="primary-title">
            O seu sistema para criar rifas beneficentes de forma <span class="underline">eficaz</span> e <span class="underline">segura</span>!
          </h1>
        </v-col>
      </v-row>

      <v-row justify="center">
        <v-col lg="6" md="10" sm="10" cols="10" order-lg="1" order-sm="12">
          <v-form class="login-form" ref="form" v-model="valid" :lazy-validation="lazy">
            <h1 class="bigger-title">Entrar</h1><br>
            
            <v-text-field 
              type="email"
              outlined
              v-model="user.email" 
              required
              label="E-mail"
              :rules="rules.email">
            </v-text-field>

            <v-text-field 
              outlined
              v-model="user.password" 
              required
              label="Senha"
              :type="showPassword ? 'text' : 'password'"
              :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
              :rules="rules.password"
              @click:append="showPassword = !showPassword">
            </v-text-field>

            <v-btn class="login-btn" color="success" block @click="login()" :disabled="isLoading">
              <v-progress-circular v-if="isLoading"
                indeterminate
                color="white"
              ></v-progress-circular>

              <span v-else>
                Vamos lá!
              </span>
            </v-btn>
            
            <div class="mt-4" v-show="errorMsg">
              <p class="text-error">{{errorMsg}}</p>
            </div>

            <div class="mt-2">
              <router-link to="/">Criar uma conta!</router-link>
            </div>
          </v-form>
        </v-col>

        <v-col class="image" lg="6" md="10" sm="10" cols="10" order-lg="12" order-sm="1">
          <img src="@/assets/winner-ticket.png"/>
        </v-col>
      </v-row>
    </v-container>
  </div>
 
</template>

<script>
// @ is an alias to /src
import api from "../plugins/axios";

export default {
  data() {
    return {
      isLoading: false,
      errorMsg: null,

      user: {
        email: '',
        password: '',
      },

      valid: true,
      lazy: false,

      showPassword: false,
      rules: {
        email: [
          v => !!v || "Informe um endereço de e-mail!",
          v => /.+@.+\..+/.test(v) || "Informe um endereço de e-mail válido!",
        ],
        password: [
          v => !!v || "Informe uma senha!",
          v => v.length >= 8 || "Informe uma senha com no mínimo 8 caracteres!",
        ]
      }
    
    }
  },
  methods: {
    login() {
      if(this.$refs.form.validate()) {
        this.isLoading = true;

        api.post('/auth/sign_in', this.user)
          .then((res) => {
            localStorage.setItem('uid', res.headers.uid);
            localStorage.setItem('client', res.headers.client);
            localStorage.setItem('token', res.headers['access-token']);
            this.$router.push('/inicio');
          })
          .catch(() => {
            this.errorMsg = "E-mail ou senha incorretos!"
            this.isLoading = false;
          })
      }
    }
  }
}
</script>

<style scoped>
  .login-link {
    margin-top: 10px;
  }
  
  .primary-title {
    margin: 25px 50px;
    font-weight: normal;
  }

  .bigger-title {
    font-size: 35pt;
  }

  .check-icon {
    color: green !important;
    font-size: 35pt !important;
  }

  .beneficios-box {
    justify-content: center;
    align-items: center;
    flex-direction: column;
    display: flex;
  }

  .beneficios {
    font-weight: 500;
    width: 70%;
    text-align: start;
  }

  .login-form {
    margin: 25px 50px;
  }

  .underline {
    text-decoration: underline;
  }

  .login-btn {
    padding: 20px;
  }
  
  .image {
    display: flex;
    justify-content: center;
  }

  .image img {
    width: 90%;
    object-fit: contain;
  }
</style>

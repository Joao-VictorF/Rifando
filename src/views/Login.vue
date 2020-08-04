<template>
  <v-container>
    <v-row justify="center">
      <v-col>
        <h1 class="primary-title">
          <strong>Rifando!</strong> O seu sistema para criar rifas beneficentes de forma <span class="underline">eficaz</span> e <span class="underline">segura</span>!
        </h1>
      </v-col>
    </v-row>


    <v-row justify="center">
      <v-col>
        <v-form class="login-form" ref="form" v-model="valid" :lazy-validation="lazy">
          <h1 class="bigger-title">Cadastre-se!</h1><br>
          
           <h2 class="title">
            <v-icon class="check-icon">
              mdi-check
            </v-icon>
            Crie os seus próprios sorteios!
          </h2><br>

          <h2 class="title">
            <v-icon class="check-icon">
              mdi-check
            </v-icon>
            Concorra a premios!
          </h2><br>

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
        </v-form>
      </v-col>

      <v-col>
        <img src="@/assets/winner.png"/>
  
        <img class="ticket" src="@/assets/ticket.png"/>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
// @ is an alias to /src

export default {
  data() {
    return {
      isLoading: false,

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

        setTimeout(() => {
          this.$router.push('/inicio');
        }, 1500)
      }
    }
  }
}
</script>

<style scoped>
  /* .form-column, .center-column {
    display: flex;
    align-items: center;
    justify-content: center;
  } */

  .primary-title {
    font-size: 40pt;
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

  .login-form {
    margin: 25px 50px;
  }

  .ticket {
    position: absolute;
    width: 200px;
    top: 550px;
    left: 75%;
  }

  .underline {
    text-decoration: underline;
  }

  .login-btn {
    padding: 20px;
  }
</style>

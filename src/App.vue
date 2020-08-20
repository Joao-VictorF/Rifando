<template>
  <v-app>
    <!-- <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div> -->
    <router-view/>
  </v-app>
</template>

<style lang="scss">
// rosa claro - #f9dbd2
// vermelho   - #ee3e54
// amarelo    - #f1bc19

  #app {
    background-color: #f3f3f3;
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }

  #nav {
    padding: 30px;

    a {
      font-weight: bold;
      color: #2c3e50;

      &.router-link-exact-active {
        color: #42b983;
      }
    }
  }

  .fixed-bar {
    z-index: 10;
  }
  .fixed-bar .v-toolbar {
    border-bottom-right-radius: 24px !important;
    z-index: 9999;
  }

  .text-error {
    color: firebrick;
  }
</style>

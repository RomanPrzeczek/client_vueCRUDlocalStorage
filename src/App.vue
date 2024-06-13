<template>

<img alt="Vue logo" src="./assets/logo.png">

<p v-if="actualPage.includes('login')">
  <LoginPage @actualPage="switchPage" @logedUser="getLogedUser"/>
  <LocalStorage />
</p>
<p v-else-if="actualPage.includes('registration')">
  <RegisPage @actualPage="switchPage"/>
  <LocalStorage />
</p>
<p v-else-if="actualPage.includes('detailing')">
  <ShowUser 
    @actualPage="switchPage" 
    v-bind:loged-user=this.logedUser    
  />
</p>

</template>

<script>
 import ShowUser from './components/ShowUser.vue'
 import LoginPage from './components/LoginPage.vue';
 import RegisPage from './components/RegisPage.vue';
 import LocalStorage from './components/LocalStorage.vue';


export default {
  name: 'App',
  data: () => {
    return {
      actualPage: 'login',
      logedUser: null,
    }
  },
  components: {
    ShowUser,
    LoginPage,
    RegisPage,
    LocalStorage
  },
  methods: {
    switchPage(receivedPage){
      this.actualPage = receivedPage;
    },
    getLogedUser(receivedUser){
      this.logedUser=receivedUser;
      return this.logedUser;
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

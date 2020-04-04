<template>
 <div class="container-fluid chat_container" id="app">
     <div class="row" v-if="authenticated">
       <Rooms />
       <Video :username="username"/>
       <Logs />
     </div>
     <div class="row" v-else>
       <div class="username">
          <center><h1>Live Class</h1></center>
           <form @submit.prevent="submitUsername(username)">
             <div class="form-group mb-2">
                 <input type="text" class="form-control" placeholder="Username" v-model="username" >
             </div>

             <div class="form-group mb-2">
                <button type="submit" class="btn btn-primary mb-2">Enter!</button>
             </div>
         </form>
       </div>
     </div>
 </div>
</template>

<script>
import Vue from 'vue';
import VueToast from 'vue-toast-notification';
 
import Rooms from './components/Rooms'
import Video from './components/Video'
import Logs from './components/Logs'
import AddRoom from './components/AddRoom'

Vue.use(VueToast);
export default {
 name: 'App',
 data() {
   return {
     username: "",
     authenticated: false
   }
 },
 components: {
   Rooms,
   Video,
   Logs,
   AddRoom
 },
 methods: {
   submitUsername(username) {
      if(!username) {
        return Vue.$toast.open({
                message: 'please provide a username',
                type: 'error',
              });
      }

      this.authenticated = true;
   }
 }
 }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: black;
  }
  h1{
    color: black;
    margin-bottom: 100px;
  }
  .box {
    border: 1px solid gray;
  }
  .username {
    margin: 100px auto 7px auto;
    color: black;
  }
</style>
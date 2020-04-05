<template>
   <div>
       <form @submit.prevent="createNewRoom(room_name)">
           <div class="form-group mb-2 classForm">
               <input type="text" class="form-control" placeholder="Class Name" v-model="room_name" >
           </div>
             <li>
              <a @click="createNewRoom(room_name)"> 
                Add New Class
              </a>
            </li> 
       </form>
   </div>
</template>

<script>
import { EventBus } from '../Event'
import Vue from 'vue';
import VueToast from 'vue-toast-notification';
Vue.use(VueToast);

export default {
 name: "AddRoom", // Component name
 data() {
   return {
    room_name: "",
   }
 },
 methods: {
   createNewRoom(name) {
       if(!name) {
          Vue.$toast.open({
                message: 'please provide a class name',
                type: 'error',
              });
         return
       }

       this.room_name = '';
       EventBus.$emit('new_room', name);
   }
 }
}
</script>

<style>
 .classForm{
  padding: 10px
  }
 .sidebar-container {
  position: fixed;
  width: 220px;
  height: 100%;
  left: 0;
  overflow-x: hidden;
  overflow-y: auto;
  background: #1a1a1a;
  color: #fff;
}

.content-container {
  padding-top: 20px;
}

.sidebar-logo {
  padding: 10px 15px 10px 30px;
  font-size: 20px;
  background-color: #351071;
}

.sidebar-navigation {
  padding: 0;
  margin: 0;
  list-style-type: none;
  position: relative;
}

.sidebar-navigation li {
  background-color: transparent;
  position: relative;
  display: inline-block;
  width: 100%;
  line-height: 20px;
}

.sidebar-navigation li a {
  padding: 10px 15px 10px 30px;
  display: block;
  color: #fff;
  text-align: left;
}

.sidebar-navigation li .fa {
  margin-right: 10px;
}

.sidebar-navigation li a:active,
.sidebar-navigation li a:hover,
.sidebar-navigation li a:focus {
  text-decoration: none;
  outline: none;
}

.sidebar-navigation li::before {
  background-color: #351071;
  position: absolute;
  content: '';
  height: 100%;
  left: 0;
  top: 0;
  -webkit-transition: width 0.2s ease-in;
  transition: width 0.2s ease-in;
  width: 3px;
  z-index: -1;
}

.sidebar-navigation li:hover::before {
  width: 100%;
}

.sidebar-navigation .header {
  font-size: 12px;
  text-transform: uppercase;
  background-color: #151515;
  padding: 10px 15px 10px 30px;
}

.sidebar-navigation .header::before {
  background-color: transparent;
}

.content-container {
  padding-left: 220px;
}
</style>
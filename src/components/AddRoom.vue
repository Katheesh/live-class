<template>
   <div class="row roomForm">
       <form @submit.prevent="createNewRoom(room_name)">
           <div class="form-group mb-2">
               <input type="text" class="form-control" v-model="room_name" >
           </div>
           <button type="submit" class="btn btn-primary mb-2">Create Class</button>
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
 .roomForm {
     margin-left: auto;
     margin-right: auto;
     margin-top: 30px;
     width: 100%;
 }

</style>
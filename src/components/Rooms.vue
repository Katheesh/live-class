<template>
 <div class="sidebar-container">
  <div class="sidebar-logo">
    Live-Class
  </div>
  <ul class="sidebar-navigation">
    <AddRoom />  
    <li class="header">Classes</li>
    <li v-for="room in rooms" v-bind:key="room.id" @click="showRoom(room.name)">
      <a href="#">
        {{room.name}}
      </a>
    </li>
     
  </ul>
</div>
</template>

<script>
import Vue from "vue";
import { EventBus } from '../Event'
import AddRoom from '../components/AddRoom'

export default {
 name: "Rooms", // Name of the component
 data() {
   return {
       rooms: [
           {id: 1, name: 'Common Class'}
       ],
       roomCount: 3, // used to keep track of the number of rooms present
       loading: false, // indicate when tracks in a room is being loaded
   }
 },
 components: {
   AddRoom // imported AddRoom component
 },
 created() {
    EventBus.$on('new_room', (data) => {
    this.roomCount++;
    this.rooms.push({id: this.roomCount, name: data});
   });
 },
 methods: {
   showRoom(room) {
       EventBus.$emit('show_room', room);
   }
 }

}
</script>

<style scoped> /* scoped attribute is used here so the styling applies to this component alone */

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
  background-color: #2574A9;
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
  background-color: #2574A9;
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
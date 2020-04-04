<template>

</template>

<script>
import Vue from 'vue';
import VueToast from 'vue-toast-notification';

import { EventBus } from '../Event'
import axios from 'axios'

Vue.use(VueToast);

export default {
  name: "Logs",
  data() {
    return {
        logs: [],
        logCount: 0
    }
  },
  created() {
    EventBus.$on('new_log', (message) => {
        Vue.$toast.open({
            message: message,
            type: 'info',
            duration: 5000
          });
        //this.logs.push( {id: this.logCount, message: message} );
        //this.logCount += 1; 
    })
  },
}
</script>

<style scoped>
    .log {
        border: 1px solid rgb(124, 129, 124);
        padding: 13px;
        margin: 3px 0px;
        color: black;
    }
</style>
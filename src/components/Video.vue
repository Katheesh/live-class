<template>
   <div  class="content-container">
       <div class="roomTitle">
           <span v-if="loading"> Loading... {{roomName}}</span>
           <span v-else-if="!loading && roomName"> Connected to {{roomName}}</span>
           <span v-else>Select a class to get started <br> <img class="banerpic" height="200" src="../assets/app-logo.png"></span>
       </div>
         <div class="row">
           <div class="cols-sm-3">
             <div class="remotevideoframe" id="remoteTrack"></div>
           </div>
           <div class="cols-sm-3">
              <div class="localvideoframe" id="localTrack"></div>   
           </div>   
         </div>       
    </div>
</template>

<script>
import { EventBus } from '../Event'
import Twilio, { connect, createLocalTracks, createLocalVideoTrack } from 'twilio-video'
import axios from 'axios'

export default {
 name: "Video",
 data() {
   return {
     loading: false,
     data: {},
     localTrack: false,
     remoteTrack: '',
     activeRoom: '',
     previewTracks: '',
     identity: '',
     roomName: null,
   }
 },
 props: ['username'], // props that will be passed to this component
 created() {
    EventBus.$on('show_room', (room_name) => {
      this.createChat(room_name);
    })

   // When a user is about to transition away from this page, 
   // disconnect from the room, if joined.
   window.addEventListener('beforeunload', this.leaveRoomIfJoined);
 },
  methods: {
    async getAccessToken() {
      return await axios.get(`https://live-class.herokuapp.com/token?identity=${this.username}`);
    },
    // Trigger log events 
   dispatchLog(message) {
       EventBus.$emit('new_log', message);
   },
    // Attach the Tracks to the DOM.
   attachTracks(tracks, container) {
       tracks.forEach(function(track) {
           container.appendChild(track.attach());
       });
   },
      // Attach the Participant's Tracks to the DOM.
   attachParticipantTracks(participant, container) {
       let tracks = Array.from(participant.tracks.values());
       this.attachTracks(tracks, container);
   },
      // Detach the Tracks from the DOM.
   detachTracks(tracks) {
       tracks.forEach( (track) => {
           track.detach().forEach((detachedElement) => {
              detachedElement.remove();
           });
       });
   },
      // Detach the Participant's Tracks from the DOM.
   detachParticipantTracks(participant) {
       let tracks = Array.from(participant.tracks.values());
       this.detachTracks(tracks);
   },
      // Leave Room.
   leaveRoomIfJoined() {
       if (this.activeRoom) {
           this.activeRoom.disconnect();
       }
   },
    createChat(room_name) {
      this.loading = true;
      const VueThis = this;

      this.getAccessToken().then( (data) => {
          VueThis.roomName = null;
          const token = data.data.token;
          let connectOptions = {
              name: room_name,
              // logLevel: 'debug',
              audio: true,
              video: { width: 200 }
          };
          // before a user enters a new room,
          // disconnect the user from they joined already
          this.leaveRoomIfJoined();
        
          // remove any remote track when joining a new room
          document.getElementById('remoteTrack').innerHTML = "";

          Twilio.connect(token , connectOptions).then(function(room) {
              // console.log('Successfully joined a Room: ', room);
              VueThis.dispatchLog('Successfully joined a class: '+ room_name);

              // set active toom
              VueThis.activeRoom = room;
              VueThis.roomName = room_name;
              VueThis.loading = false;

              // Attach the Tracks of the Room's Participants.
               room.participants.forEach(function(participant) {
                   let previewContainer = document.getElementById('remoteTrack');
                   VueThis.attachParticipantTracks(participant, previewContainer);
               });

               // When a Participant joins the Room, log the event.
               room.on('participantConnected', function(participant) {
                   VueThis.dispatchLog("Joining: '" + participant.identity + "'");
               });

               // When a Participant adds a Track, attach it to the DOM.
               room.on('trackAdded', function(track, participant) {
                   VueThis.dispatchLog(participant.identity + " added track: " + track.kind);
                   let previewContainer = document.getElementById('remoteTrack');
                   VueThis.attachTracks([track], previewContainer);
               });

               // When a Participant removes a Track, detach it from the DOM.
               room.on('trackRemoved', function(track, participant) {
                   VueThis.dispatchLog(participant.identity + " removed track: " + track.kind);
                   VueThis.detachTracks([track]);
               });

                              // When a Participant leaves the Room, detach its Tracks.
               room.on('participantDisconnected', function(participant) {
                   VueThis.dispatchLog("Participant '" + participant.identity + "' left the class");
                   VueThis.detachParticipantTracks(participant);
               });

                // if local preview is not active, create it
               if(!VueThis.localTrack) {

                   createLocalVideoTrack().then(track => {
                     let localMediaContainer = document.getElementById('localTrack');

                     localMediaContainer.appendChild(track.attach());
                     VueThis.localTrack = true;
                   });
               }

         });
      })
   },

   }
}
</script>

<style >
    .remotevideoframe{
      position: absolute;
      height: auto !important;
      margin-left: 200px;
      display: inline-block;
      left: 50px;
    }

    .localvideoframe{
      position: absolute;
      height: auto !important;
      margin-left: 200px;
      display: block;
      right: 20px;
    }
    #localTrack video {
      margin: 0px;
      max-width: 200px !important;
      background-repeat: no-repeat; 
      border-radius: 15px;
      border: 3px solid #351071;   
    }
    #remoteTrack video{
      margin: 10px;
      max-width: 200px !important;
      background-repeat: no-repeat; 
      border-radius: 15px;
      border: 3px solid #351071;   
    }
    .roomTitle {
      text-align: center;
      padding: 4px;
      color: black;
    }
    .content-container {
      padding-top: 20px;
    }
    .content-container {
      padding-left: 220px;
    }
    .banerpic{
      margin-top: 175px;
    }
</style>
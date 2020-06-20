<template>
  <v-container class="custom-theme custom-tab-body" fill-height fluid>
    <v-row justify="center" align="center" class="text-center">
      <v-col cols="12">
        <h1 class="display-4 font-weight-bold mb-1">
          <v-row class="mx-auto" align="center" justify="center">
            <div class="pa-0 ma-0" contenteditable @blur="setHour" :key="editingHour + '-Hour'">{{displayHour}}</div>:
            <div class="pa-0 ma-0" contenteditable @blur="setMinute" :key="editingMinute + '-Minute'">{{displayMinute}}</div>:
            <div class="pa-0 ma-0" contenteditable @blur="setSecond" :key="editingSecond + '-Second'">{{displaySecond}}</div>
          </v-row>
        </h1>
        <audio id="audio" src="../assets/Electronic_Chime-KevanGC-495939803.mp3"></audio>
      </v-col>
      <v-checkbox v-model="repeatTime" color="#4a7489" class="mx-4" label="Repeat"></v-checkbox>
      <v-checkbox v-model="ringAlarm" color="#4a7489" class="mx-4" label="Play Alarm"></v-checkbox>
      <v-checkbox
        v-model="displayNotification"
        color="#4a7489"
        class="mx-4"
        label="Display Notification"
      ></v-checkbox>
      <v-col cols="12">
        <v-btn v-if="!this.countingDown" dark color="#4a7489" @click="startTimer">
          <v-icon left>mdi-alarm</v-icon>Start
        </v-btn>
        <div v-else class="pa-0 ma-0">
          <v-btn v-if="!paused" class="mx-2" dark color="#4a7489" @click="pauseTimer">
            <v-icon left>mdi-pause</v-icon>Pause
          </v-btn>
          <v-btn v-else class="mx-2" dark color="#4a7489" @click="resumeTimer">
            <v-icon left>mdi-play</v-icon>Resume
          </v-btn>
          <v-btn class="mx-2" dark color="#4a7489" @click="stopTimer">
            <v-icon left>mdi-alarm-off</v-icon>Stop
          </v-btn>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "CustomTimer",

  data: () => ({
    totalTime: 1800,
    startHour: 0,
    startMinute: 30,
    startSecond: 0,
    editingHour: false,
    editingMinute: false,
    editingSecond: false,
    timerFunction: null,
    countingDown: false,
    paused: false,
    repeatTime: true,
    ringAlarm: true,
    displayNotification: true
  }),
  computed: {
    displayHour: function() {
      return ("0" + Math.floor(this.totalTime / 3600)).slice(-2);
    },
    displayMinute: function() {
      return ("0" + Math.floor((this.totalTime % 3600) / 60)).slice(-2);
    },
    displaySecond: function() {
      console.log("Recalculating Seconds")
      return ("0" + Math.floor((this.totalTime % 3600) % 60)).slice(-2);
    },
    displayTime: function() {
      return (
        ("0" + this.displayHour).slice(-2) +
        ":" +
        ("0" + this.displayMinute).slice(-2) +
        ":" +
        ("0" + this.displaySecond).slice(-2)
      );
    }
  },
  methods: {
    calculateTotalTime(){
      this.totalTime =
        3600 * this.startHour + 60 * this.startMinute + this.startSecond;
      console.log(this.totalTime);
    },
    startTimer() {
      this.countingDown = true;
      this.timerFunction = setInterval(() => {
        this.totalTime -= 1;
        console.log(this.totalTime)
        console.log(this.displaySecond)
        if (this.totalTime < 1) {
          if (this.displayNotification) {
            this.pushNotification();
          }
          if (this.ringAlarm) {
            this.playAlarm();
          }
          if (this.repeatTime) {
            this.totalTime =
              3600 * this.startHour + 60 * this.startMinute + this.startSecond;
          } else {
            this.stopTimer();
          }
        }
      }, 1000);
    },
    stopTimer() {
      clearInterval(this.timerFunction);
      this.calculateTotalTime();
      this.countingDown = false;
    },
    pauseTimer() {
      clearInterval(this.timerFunction);
      this.paused = true;
    },
    resumeTimer() {
      this.startTimer();
      this.paused = false;
    },
    playAlarm() {
      console.log("BRRIIING");
      const audio = document.getElementById("audio");
      audio.play();
    },
    pushNotification() {
      console.log("AYO YOUR TIME UP");
    },
    setHour(event) {
      let source = parseInt("0" + event.target.textContent);
      if(!isNaN(source)){
        if(source > 59){
          source = 59
        }
        this.startHour = source;
      }
      this.calculateTotalTime();
      this.editingHour = !this.editingHour;
    },
    setMinute(event) {
      let source = parseInt("0" + event.target.textContent);
      if(!isNaN(source)){
        if(source > 59){
          source = 59
        }
        this.startMinute = source;
      }
      this.calculateTotalTime();
      this.editingMinute = !this.editingMinute;
    },
    setSecond(event) {
      let source = parseInt("0" + event.target.textContent);
      if(!isNaN(source)){
        if(source > 59){
          source = 59
        }
        this.startSecond = source;
      }
      this.calculateTotalTime();
      this.editingSecond = !this.editingSecond;
    }
  }
};
</script>
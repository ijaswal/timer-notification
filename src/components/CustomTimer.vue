<template>
  <v-main class="custom-theme custom-tab-body">
  <v-row align="center" justify="center" class="text-center">
    <v-checkbox v-model="repeatTime" color="#4a7489" class="mx-4" label="Repeat"></v-checkbox>
      <v-checkbox v-model="ringAlarm" color="#4a7489" class="mx-4" label="Play Alarm"></v-checkbox>
      <!-- <v-checkbox
        v-model="displayNotification"
        color="#4a7489"
        class="mx-4"
        label="Display Notification"
      ></v-checkbox>-->
        
  </v-row>
    <v-row justify="center" align="center" class="text-center">
      <v-col cols="12">
        <h1 class="display-4 font-weight-bold mb-1">
          <v-row v-if="editableTimer" class="mx-auto" align="center" justify="center">
            <div
              class="timer-input pa-0 ma-0"
              contenteditable
              @blur="setHour"
              :key="editingHour + '-Hour'"
            >{{displayHour}}</div>:
            <div
              class="timer-input pa-0 ma-0"
              contenteditable
              @blur="setMinute"
              :key="editingMinute + '-Minute'"
            >{{displayMinute}}</div>:
            <div
              class="timer-input pa-0 ma-0"
              contenteditable
              @blur="setSecond"
              :key="editingSecond + '-Second'"
            >{{displaySecond}}</div>
          </v-row>
          <v-row v-else class="mx-auto" align="center" justify="center">
            <div
              class="timer-input pa-0 ma-0"
            >{{displayHour}}</div>:
            <div
              class="timer-input pa-0 ma-0"
            >{{displayMinute}}</div>:
            <div
              class="timer-input pa-0 ma-0"
            >{{displaySecond}}</div>
          </v-row>
        </h1>
        <audio id="audio" src="../assets/Electronic_Chime-KevanGC-495939803.mp3"></audio>
      </v-col>
      <v-col cols="12">
        <v-btn v-if="!this.countingDown" dark color="#4a7489" @click="startTimer">
          <v-icon left>mdi-alarm</v-icon>Start
        </v-btn>
        <div v-else class="pa-0 ma-0">
          <v-btn v-if="!pausedTimer" class="mx-2" dark color="#4a7489" @click="pauseTimer">
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
  </v-main>
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
    pausedTimer: false,
    repeatTime: true,
    ringAlarm: true,
    displayNotification: true
  }),
  computed: {
    editableTimer: function() {
      return !this.countingDown || this.pausedTimer
    },
    displayHour: function() {
      return ("0" + Math.floor(this.totalTime / 3600)).slice(-2);
    },
    displayMinute: function() {
      return ("0" + Math.floor((this.totalTime % 3600) / 60)).slice(-2);
    },
    displaySecond: function() {
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
    calculateTotalTime() {
      this.totalTime =
        3600 * this.startHour + 60 * this.startMinute + this.startSecond;
    },
    startTimer() {
      this.countingDown = true;
      this.timerFunction = setInterval(() => {
        this.totalTime -= 1;
        if (this.totalTime < 1) {
          // if (this.displayNotification) {
          //   this.pushNotification();
          // }
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
      this.pausedTimer = true;
    },
    resumeTimer() {
      this.startTimer();
      this.pausedTimer = false;
    },
    playAlarm() {
      const audio = document.getElementById("audio");
      audio.play();
    },
    // pushNotification() {
    //   console.log("Your time has come");
    // },
    setHour(event) {
      let source = parseInt("0" + event.target.textContent);
      if (!isNaN(source)) {
        if (source > 59) {
          source = 59;
        }
        this.startHour = source;
      }
      this.calculateTotalTime();
      this.editingHour = !this.editingHour;
    },
    setMinute(event) {
      let source = parseInt("0" + event.target.textContent);
      if (!isNaN(source)) {
        if (source > 59) {
          source = 59;
        }
        this.startMinute = source;
      }
      this.calculateTotalTime();
      this.editingMinute = !this.editingMinute;
    },
    setSecond(event) {
      let source = parseInt("0" + event.target.textContent);
      if (!isNaN(source)) {
        if (source > 59) {
          source = 59;
        }
        this.startSecond = source;
      }
      this.calculateTotalTime();
      this.editingSecond = !this.editingSecond;
    }
  }
};
</script>
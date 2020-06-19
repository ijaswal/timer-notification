<template>
  <v-container class="pomodoro-theme pomodoro-tab-body" fill-height fluid>
    <v-row align="center" justify="center" class="text-center">
      <v-checkbox v-model="ringAlarm" color="#d04343" class="mx-4" label="Play Alarm"></v-checkbox>
      <v-checkbox
        v-model="displayNotification"
        color="#d04343"
        class="mx-4"
        label="Display Notification"
      ></v-checkbox>
    </v-row>
    <v-row align="center" justify="center" class="text-center">
      <v-card flat class="px-12 py-3 mb-12" color="#fb4f4f">
        <v-row>
          <v-col cols="10" class="my-auto">
            <h1 class="display-3 font-weight-bold">{{displayTime}}</h1>
            <audio id="audio" src="../assets/Electronic_Chime-KevanGC-495939803.mp3"></audio>
            <v-col cols="12">
              <v-btn v-if="!this.countingDown" dark color="#d04343" @click="startTimer">
                <v-icon left>mdi-alarm</v-icon>Start
              </v-btn>
              <v-btn v-else dark color="#4a7489" @click="stopTimer">
                <v-icon left>mdi-alarm-off</v-icon>Stop
              </v-btn>
            </v-col>
          </v-col>
          <v-col cols="1" class="my-auto">
            <v-checkbox readonly color="#381212" v-model="pomodoroArray[0]" class="ma-0 pa-0"></v-checkbox>
            <v-checkbox readonly color="#381212" v-model="pomodoroArray[1]" class="ma-0 pa-0"></v-checkbox>
            <v-checkbox readonly color="#381212" v-model="pomodoroArray[2]" class="ma-0 pa-0"></v-checkbox>
            <v-checkbox readonly color="#381212" v-model="pomodoroArray[3]" class="ma-0 pa-0"></v-checkbox>
          </v-col>
        </v-row>
      </v-card>
    </v-row>
    <v-row align="center" justify="center" class="text-center">
      <v-card flat class="pa-12 mb-12" color="#fb4f4f">
        <v-col cols="12">
          <h1 class="display-3 font-weight-bold">{{displayShortTime}}</h1>
        </v-col>
      </v-card>
    </v-row>
    <v-row align="center" justify="center" class="text-center">
      <v-card flat class="pa-12 mb-12" color="#fb4f4f">
        <v-col cols="12">
          <h1 class="display-3 font-weight-bold">{{displayLongTime}}</h1>
        </v-col>
      </v-card>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "PomodoroTimer",

  data: () => ({
    pomodoroCount: 0,
    pomodoroArray: [false, false, false, false],
    totalTime: 3,
    totalShortTime: 5,
    totalLongTime: 10,
    startHour: 0,
    startMinute: 0,
    startSecond: 3,
    startShortHour: 0,
    startShortMinute: 0,
    startShortSecond: 5,
    startLongHour: 0,
    startLongMinute: 0,
    startLongSecond: 10,
    timerFunction: null,
    shortBreakFunction: null,
    longBreakFunction: null,
    countingDown: false,
    repeatTime: true,
    ringAlarm: true,
    displayNotification: true
  }),
  computed: {
    displayTime: function() {
      return (
        ("0" + this.getHours(this.totalTime)).slice(-2) +
        ":" +
        ("0" + this.getMinutes(this.totalTime)).slice(-2) +
        ":" +
        ("0" + this.getSeconds(this.totalTime)).slice(-2)
      );
    },
    displayShortTime: function() {
      return (
        ("0" + this.getHours(this.totalShortTime)).slice(-2) +
        ":" +
        ("0" + this.getMinutes(this.totalShortTime)).slice(-2) +
        ":" +
        ("0" + this.getSeconds(this.totalShortTime)).slice(-2)
      );
    },
    displayLongTime: function() {
      return (
        ("0" + this.getHours(this.totalLongTime)).slice(-2) +
        ":" +
        ("0" + this.getMinutes(this.totalLongTime)).slice(-2) +
        ":" +
        ("0" + this.getSeconds(this.totalLongTime)).slice(-2)
      );
    }
  },
  methods: {
    startTimer() {
      clearInterval(this.shortBreakFunction);
      clearInterval(this.longBreakFunction);
      this.countingDown = true;
      this.timerFunction = setInterval(() => {
        this.totalTime -= 1;
        if (this.totalTime < 1) {
          if (this.displayNotification) {
            this.pushNotification();
          }
          if (this.ringAlarm) {
            this.playAlarm();
          }
          console.log(this.pomodoroCount);
          this.pomodoroArray[this.pomodoroCount] = true;
          this.pomodoroCount++;
          if (this.pomodoroCount < 4) {
            this.startShortBreak();
          } else {
            this.startLongBreak();
          }
          this.totalTime =
            3600 * this.startHour + 60 * this.startMinute + this.startSecond;
        }
      }, 1000);
    },
    stopTimer() {
      clearInterval(this.timerFunction);
      clearInterval(this.shortBreakFunction);
      clearInterval(this.longBreakFunction);
      this.totalTime =
        3600 * this.startHour + 60 * this.startMinute + this.startSecond;
      this.totalShortTime =
        3600 * this.startShortHour +
        60 * this.startShortMinute +
        this.startShortSecond;
      this.totalLongTime =
        3600 * this.startLongHour +
        60 * this.startLongMinute +
        this.startLongSecond;
      this.countingDown = false;
      this.pomodoroCount = 0;
      this.pomodoroArray = [false, false, false, false];
    },
    startShortBreak() {
      clearInterval(this.timerFunction);
      this.shortBreakFunction = setInterval(() => {
        this.totalShortTime -= 1;
        if (this.totalShortTime < 1) {
          if (this.displayNotification) {
            this.pushNotification();
          }
          if (this.ringAlarm) {
            this.playAlarm();
          }
          this.totalShortTime =
            3600 * this.startShortHour +
            60 * this.startShortMinute +
            this.startShortSecond;
          this.startTimer();
        }
      }, 1000);
    },
    startLongBreak() {
      clearInterval(this.timerFunction);
      this.longBreakFunction = setInterval(() => {
        this.totalLongTime -= 1;
        if (this.totalLongTime < 1) {
          if (this.displayNotification) {
            this.pushNotification();
          }
          if (this.playAlarm) {
            this.playAlarm();
          }
          this.totalLongTime =
            3600 * this.startLongHour +
            60 * this.startLongMinute +
            this.startLongSecond;
          this.pomodoroCount = 0;
          this.pomodoroArray = [false, false, false, false];
          this.startTimer();
        }
      }, 1000);
    },
    playAlarm() {
      console.log("BRRIIING");
      const audio = document.getElementById("audio");
      audio.play();
    },
    pushNotification() {
      console.log("AYO YOUR TIME UP");
    },
    getHours(totalSeconds) {
      return Math.floor(totalSeconds / 3600);
    },
    getMinutes(totalSeconds) {
      return Math.floor((totalSeconds % 3600) / 60);
    },
    getSeconds(totalSeconds) {
      return Math.floor((totalSeconds % 3600) % 60);
    }
  }
};
</script>
<template>
  <v-app>
    <v-main :class="{'custom-theme': tab===0, 'pomodoro-theme': tab===1}">
      <v-tabs fixed-tabs dark :background-color="this.tabColor" v-model="tab">
        <v-tab key="custom">Custom</v-tab>
        <v-tab key="pomodero">Pomodoro</v-tab>
      </v-tabs>
      <v-tabs-items v-model="tab">
        <v-tab-item key="custom">
          <CustomTimer></CustomTimer>
        </v-tab-item>
        <v-tab-item key="pomodero">
          <PomodoroTimer></PomodoroTimer>
        </v-tab-item>
      </v-tabs-items>
    </v-main>
  </v-app>
</template>

<script>
import CustomTimer from "./components/CustomTimer";
import PomodoroTimer from "./components/PomodoroTimer";

export default {
  name: "App",

  components: {
    CustomTimer,
    PomodoroTimer
  },

  data: () => ({
    tab: 0
  }),
  computed: {
    tabColor: function() {
      return this.tab === 0 ? "#6cc0e5" : "#F14C4C";
    }
  }
};
</script>
<style>
.custom-theme {
  background-color: #6cc0e5;
  color: #313b44;
}

.pomodoro-theme {
  background-color: #f14c4c;
  color: #381212;
}

.custom-dark-theme {
  background-color: #4a7489;
  color: #313b44;
}
.pomodoro-dark-theme {
  background-color: #d04343;
  color: #381212;
}

.custom-tab-body {
  height: calc(100vh - 48px);
}

.pomodoro-tab-body {
  height: calc(100% - 48px);
}

.timer-input * { 
  display: inline 
}
</style>
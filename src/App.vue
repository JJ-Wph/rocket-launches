<template>
  <div v-for="launch in rocketsList" :key="launch.name">
    <p>{{launch.name}}</p>
    <p>{{launch.launch_service_provider.name}}</p>
    <p><b>Launch location:</b> {{launch.pad.location.name}}</p>
    <h1>{{countdownTime(launch.net)}}</h1>
    <h4>Days : Hours : Min : Sec</h4>
    <hr>
  </div>
</template>

<script>
  import {onMounted, onUnmounted, ref} from 'vue'
  import axios from 'axios'
  export default {
  name: 'App',

  setup(){
    const rocketsList = ref('');
    const currentDate = ref('');
    let days = ref(0);
    let hours = ref(0);
    let minutes = ref(0);
    let seconds = ref(0);

    function currentMoment() {
      currentDate.value = new Date()
    }

    function getData() {
      axios.get('https://lldev.thespacedevs.com/2.2.0/launch/upcoming/')
      .then(response => {
        rocketsList.value = response.data.results;
      })
    }

    function recalcRemainingTimeForRockets() {
      for(let i = 0; i < rocketsList.value[i].net.length; i++) {
        rocketsList.value[i].net = new Date(rocketsList.value[i].net);
        rocketsList.value[i].net = rocketsList.value[i].net - currentDate.value;
      }
    }

    function countdownTime(v) {
        v = v - currentDate.value;
        seconds.value = parseInt(v/1000);
        minutes.value = parseInt(seconds.value/60);
        hours.value = parseInt(minutes.value/60);
        days.value = parseInt(hours.value/24);
        return days.value + '  :  ' + hours.value % 24 + '  :  ' + minutes.value % 60 + '  :  ' + seconds.value % 60;
    }

    const countdownInterval = setInterval(currentMoment, 1000);
    const recalcTimeout = setTimeout(recalcRemainingTimeForRockets, 500)

    onMounted(() => {
      getData()
    });

    onUnmounted(() => {
      clearInterval(countdownInterval)
    })

    return {
      getData,
      currentMoment,
      rocketsList,
      recalcRemainingTimeForRockets,
      countdownTime,
      countdownInterval,
      recalcTimeout,
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

<template>
  <div class="launchCard" v-for="launch in rocketsList" :key="launch.name">
    <p><b>{{launch.name}}</b></p>
    <img :src="launch.image" alt="rocket img">
    <p>{{launch.launch_service_provider.name}}</p>
    <p><b>Launch location:</b> {{launch.pad.location.name}}</p>
    <div class="countdownDiv">
      <h1>{{countdownTime(launch.net)}}</h1>
      <h2>Days : Hrs : Min : Sec</h2>
    </div>
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
        console.log(rocketsList.value);
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

        hours.value = hours.value % 24;
        minutes.value = minutes.value % 60
        seconds.value = seconds.value % 60

        if(seconds.value <= 9){
          seconds.value = "0" + seconds.value
        }

        if(minutes.value <= 9){
          minutes.value = "0" + minutes.value
        }

        if(hours.value <= 9){
          hours.value = "0" + hours.value
        }

        if(days.value <= 9){
          days.value = "0" + days.value
        }

        return days.value + ' : ' + hours.value + '  :  ' + minutes.value + '  :  ' + seconds.value;
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
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: rgb(216, 221, 227);
  height: 100%;
  width: 100%;

}

.launchCard{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
  background: rgba( 255, 255, 255, 0.25 );
  box-shadow: 0 8px 32px 0 rgba( 31, 38, 135, 0.37 );
  backdrop-filter: blur( 14.5px );
  -webkit-backdrop-filter: blur( 14.5px );
  border-radius: 10px;
  border: 1px solid rgba( 255, 255, 255, 0.18 );
  margin: 16px 10px 16px 10px;
  width: 350px;
}

img {
  width: 10rem;
  height: 10rem;
}

p {
  margin: 0.5rem
}

.countdownDiv > h1, h2{
  margin: 0;
  letter-spacing: 4px;
}

.countdownDiv > h3{
  width: 0 15px 0 15px;
}

</style>

<template>
  <div class="launchCard" v-for="launch in rocketsList" :key="launch.name">
    <p><b>{{launch.name}}</b></p>
    <img :src="launch.image" alt="rocket img">
    <p>{{launch.launch_service_provider.name}}</p>
    <p><b>Launch location:</b> {{launch.pad.location.name}}</p>
    <div class="countdownDiv">
    <h2>{{launch.launch_time}}</h2>
    <h2>{{dateFormat}}</h2>
    </div>
  </div>
</template>

<script>
  import {onUnmounted, ref} from 'vue'
  import axios from 'axios'
  export default {
  name: 'App',

  setup(){
    let rocketsList = ref(null);
    let launchDate = ref(null);
    let currentDate = ref(null);
    let dateFormat = ref(0);
    let days = ref(0);
    let hours = ref(0);
    let minutes = ref(0);
    let seconds = ref(0);

    const currentMoment = setInterval(function() {
        currentDate.value = new Date().getTime();
    }, 1000)

    function getData() {
        axios.get('https://ll.thespacedevs.com/2.2.0/launch/upcoming/?limit=6')
        .then(response => {
          rocketsList.value = response.data.results;
          console.log(rocketsList.value);
        })
      }

    const recalcRemainingTimeForRockets = setInterval (function() {
      for(let i = 0; i <= rocketsList.value[i].net.length; i++) {
          launchDate.value = new Date(rocketsList.value[i].net).getTime();
          launchDate.value = launchDate.value - currentDate.value;
          console.log(launchDate.value);
          days.value = Math.floor(launchDate.value / (1000*60*60*24));
          hours.value = Math.floor((launchDate.value % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
          minutes.value = Math.floor((launchDate.value % (1000 * 60 * 60)) / (1000 * 60));
          seconds.value = Math.floor((launchDate.value % (1000 * 60)) / 1000);

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

          if(launchDate.value < 0) {
            rocketsList.value[i].launch_time = 'Launched';
            dateFormat.value = ''
          } else {
            rocketsList.value[i].launch_time = days.value + ' : ' + hours.value + '  :  ' + minutes.value + '  :  ' + seconds.value;
            dateFormat.value = 'Days : Hrs : Min : Sec';
          }
      }
    }, 1000)

    const getDataTimeout = setTimeout(getData, 200)
    const recalcTimeout = setTimeout(recalcRemainingTimeForRockets, 1500)

    onUnmounted(() => {
      clearInterval(recalcTimeout)
    })

    return {
      getData,
      currentDate,
      launchDate,
      dateFormat,
      currentMoment,
      rocketsList,
      recalcRemainingTimeForRockets,
      getDataTimeout,
      recalcTimeout,
      days,
      hours,
      minutes,
      seconds
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

.countdownDiv {
  display: flex;
  flex-direction: column;
}

.countdownDiv > h1, h2{
  margin: 0;
  letter-spacing: 4px;
}

.countdownDiv > h3{
  width: 0 15px 0 15px;
}

</style>

<template>
  <div class="launchCard" v-for="launch in rocketsList" :key="launch.name">
    <p><b>{{launch.name}}</b></p>
    <img :src="launch.image" alt="rocket img">
    <p>{{launch.launch_service_provider.name}}</p>
    <p><b>Launch location:</b> {{launch.pad.location.name}}</p>
    <div class="countdownDiv" v-if="!launch.isLaunched">
      <div>
        <h2> {{launch.launch_time_days}}</h2>
        <p>Days</p>
      </div>
      <div>
        <h2>:</h2>
      </div>
      <div>
        <h2>{{launch.launch_time_hours}}</h2>
        <p>Hours</p>
      </div>
      <div>
        <h2>:</h2>
      </div>
      <div>
        <h2>{{launch.launch_time_minutes}}</h2>
        <p>Mins</p>
      </div>
      <div>
        <h2>:</h2>
      </div>
      <div>
        <h2>{{launch.launch_time_seconds}}</h2>
        <p>Secs</p>
      </div>
    </div>
    <h1 v-else>ROCKET LAUNCHED</h1>
  </div>
</template>

<script>
import {onUnmounted, ref} from 'vue'
import axios from 'axios'
export default {
    name: 'RocketCard',
    setup(){
    let rocketsList = ref('');
    let launchDate = ref('');
    let currentDate = ref('');
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
        })
      }

    const recalcRemainingTimeForRockets = setInterval (function() {
      for(let i = 0; i < rocketsList.value.length; i++) {
          launchDate.value = new Date(rocketsList.value[i].net).getTime();
          launchDate.value = launchDate.value - currentDate.value;
          days.value = Math.floor(launchDate.value / (1000 * 60 * 60 * 24));
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

          if(launchDate.value > 0) {
            rocketsList.value[i].launch_time_days = days.value;
            rocketsList.value[i].launch_time_hours = hours.value; 
            rocketsList.value[i].launch_time_minutes = minutes.value;
            rocketsList.value[i].launch_time_seconds = seconds.value;
            rocketsList.value[i].isLaunched = false;

          } else {
            rocketsList.value[i].isLaunched = true;
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
      currentMoment,
      rocketsList,
      recalcRemainingTimeForRockets,
      getDataTimeout,
      recalcTimeout,
      days,
      hours,
      minutes,
      seconds,
    }
  }
}
</script>

<style scoped>
.launchCard{
    display: flex;
    flex: 1 1 30%;
    flex-direction: column;
    align-items: center;
    justify-content: space-around;
    background: rgba( 255, 255, 255, 0.25 );
    box-shadow: 0 8px 32px 0 rgba( 31, 38, 135, 0.37 );
    backdrop-filter: blur( 14.5px );
    -webkit-backdrop-filter: blur( 14.5px );
    border-radius: 10px;
    border: 1px solid rgba( 255, 255, 255, 0.18 );
    margin: 1rem 0.6rem 1rem 0.6rem;
  }

  img {
    width: 11rem;
    height: 11rem;
  }

  p {
    margin: 0.5rem
  }

  .countdownDiv {
    display: flex;
    flex-direction: row;
    margin: 0;
  }

  .countdownDiv > div {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin: 0;
  }

  .countdownDiv > div:nth-child(odd) {
    margin: 0.5rem;
  }

  .countdownDiv > div:nth-child(even) {
    margin-bottom: 1.25rem;
  }
  .countdownDiv > div > h2 {
    margin: 0;
  }

  .countdownDiv > div > p {
    margin: 0;
  }

  .countdownDiv > h1, h2{
    letter-spacing: 4px;
  }

</style>
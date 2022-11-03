<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <div v-for="launch in dataFromApi" :key="launch.net">
    <p>{{launch.name}}</p>
    <p>{{launch.launch_service_provider.name}}</p>
    <p>{{launch.net}}</p>
    <h1>{{countdownTime(launch.net)}}</h1>
    <hr>
  </div>
</template>

<script>
  import {onMounted, ref} from 'vue'
  import axios from 'axios'
  export default {
  name: 'App',

  setup(){
    const dataFromApi = ref();

    const getData = () => {
      axios.get('https://lldev.thespacedevs.com/2.2.0/launch/upcoming/?agency_launch_attempt_count=&agency_launch_attempt_count__gt=&agency_launch_attempt_count__gte=&agency_launch_attempt_count__lt=&agency_launch_attempt_count__lte=&agency_launch_attempt_count_year=&agency_launch_attempt_count_year__gt=&agency_launch_attempt_count_year__gte=&agency_launch_attempt_count_year__lt=&agency_launch_attempt_count_year__lte=&format=json&location_launch_attempt_count=&location_launch_attempt_count__gt=&location_launch_attempt_count__gte=&location_launch_attempt_count__lt=&location_launch_attempt_count__lte=&location_launch_attempt_count_year=&location_launch_attempt_count_year__gt=&location_launch_attempt_count_year__gte=&location_launch_attempt_count_year__lt=&location_launch_attempt_count_year__lte=&mission__orbit__name=&mission__orbit__name__icontains=&name=&orbital_launch_attempt_count=&orbital_launch_attempt_count__gt=&orbital_launch_attempt_count__gte=&orbital_launch_attempt_count__lt=&orbital_launch_attempt_count__lte=&orbital_launch_attempt_count_year=&orbital_launch_attempt_count_year__gt=&orbital_launch_attempt_count_year__gte=&orbital_launch_attempt_count_year__lt=&orbital_launch_attempt_count_year__lte=&pad_launch_attempt_count=&pad_launch_attempt_count__gt=&pad_launch_attempt_count__gte=&pad_launch_attempt_count__lt=&pad_launch_attempt_count__lte=&pad_launch_attempt_count_year=&pad_launch_attempt_count_year__gt=&pad_launch_attempt_count_year__gte=&pad_launch_attempt_count_year__lt=&pad_launch_attempt_count_year__lte=&r_spacex_api_id=&rocket__configuration__full_name=&rocket__configuration__full_name__icontains=&rocket__configuration__id=&rocket__configuration__manufacturer__name=&rocket__configuration__manufacturer__name__icontains=&rocket__configuration__name=&rocket__spacecraftflight__spacecraft__id=&rocket__spacecraftflight__spacecraft__name=&rocket__spacecraftflight__spacecraft__name__icontains=&slug=&status=1')
      .then(response => {
        dataFromApi.value = response.data.results;
      })
    }

    // function countDown() {

    //   for(let i = 0; i < dataFromApi.value[i].net.length; i++) {
    //     let launchDate = dataFromApi.value[i].net;

    //     // let convertedDate = new Date(launchDate)
    //     // let timeToLaunch = convertedDate - currentDate;

    //   }
    // }

    function countdownTime(value) {
      let currentDate = new Date();
      let convertedDate = new Date(value);
      let timeToLaunch = convertedDate - currentDate;
      let days = ref(0);
      let hours = ref(0);
      let minutes = ref(0);
      let seconds = ref(0);
      seconds.value = parseInt(timeToLaunch/1000);
      minutes.value = parseInt(seconds.value/60);
      hours.value = parseInt(minutes.value/60);
      days.value = parseInt(hours.value/24);
      return value = days.value + ':' + hours.value % 24 + ':' + minutes.value % 60 + ':' + seconds.value % 60;
    }

    setInterval(countdownTime, 1000);

    onMounted(() => {
      getData()
    });

    return {
      dataFromApi,
      countdownTime,
      getData
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

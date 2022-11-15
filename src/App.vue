<template>
  <TheHeader/>
  <section>
    <RocketCard/>
  </section>  
</template>

<script>
  import TheHeader from './components/TheHeader.vue'
  import RocketCard from './components/RocketCard.vue'
  export default {
    name: 'App',
    components: {
      TheHeader,
      RocketCard
    },
}
</script>

<style>
  #app {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: rgb(216, 221, 227);
    height: 100%;
    width: 100%;
  }

  section {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    align-items: center;
    justify-content: center;
    margin: 0;
    padding: 0;
  }
</style>

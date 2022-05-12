<template>
  <WeatherInfo />
</template>

<script>
import WeatherInfo from './components/WeatherInfo.vue'

export default ({
  name: 'App',
  components: {
    WeatherInfo
  }
})
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins&display=swap');

#app {
  font-family: 'Poppins', sans-serif;
}
</style>

<template>
<div>
  <Map></Map>
</div>
</template>

<script>
import Map from './components/Map.vue'

export default {
  name: 'App',
  components: {
    Map
  },
  data () {
    return {
      data: []
    }
  },
  created() {
    // this.data = [
    //   {
    //       title: '월급날',
    //       date: '2022-05-15',
    //       color: 'blue'
    //   },
    //   {
    //       title: '생일',
    //       date: '2022-05-25',
    //       color: 'green'
    //   },
    //   {
    //     title: '여름 휴가',
    //     date: '2022-05-01~2022-05-06',
    //     color: 'yellow'
    //   }
    // ]
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

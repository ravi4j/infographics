<template>
  <div id="app">
    <RoutingMap origin ="INDEPENDENCE,JACKSON,MO,64058,USA" destination="1701 BROAD ST,STORY CITY,IA,50248 , USA" />
  </div>
</template>

<script>
import RoutingMap from "./components/RoutingMap.vue";
import axios from "axios";
export default {
  name: "app",
  components: {
    RoutingMap
  },
  data(){
    return {
    data: null
    }
  },
  async created(){
      await axios
      .get(`/static/data.json`)
      .then(response => {
        // JSON responses are automatically parsed.
        this.data = response.data;
      })
      .catch(e => {
        this.errors.push(e);
      });
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

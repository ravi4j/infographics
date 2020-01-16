<template>
  <div id="app">
    <b-card title="Card Title" no-body>
      <b-card-header header-tag="nav">
        <b-nav card-header tabs>
          <b-nav-item
            v-for="item in items"
            v-bind:key="item.id"
            :active="item.state"
            @click="navigateTo(item.id)"
          >{{item.label}}</b-nav-item>
        </b-nav>
      </b-card-header>
      <b-card-body class="text-center">
        <b-card-text></b-card-text>
        <RoutingMap v-if="items && items[0].state" 
          origin="INDEPENDENCE,JACKSON,MO,64058,USA"
          destination="1701 BROAD ST,STORY CITY,IA,50248 , USA"
        />
        <div v-if="items && items[1].state" >
          <ScatterChart :payload="data.charts" />
        </div>
        <div v-if="items && items[2].state" >
          <Gauge  label="Risk" v-bind:min="0" v-bind:max="25" v-bind:value="9" />
        </div>
      </b-card-body>
    </b-card>
  </div>
</template>

<script>
import RoutingMap from "./components/RoutingMap.vue";
import ScatterChart from "./components/ScatterChart.vue";
import Gauge from "./components/Gauge.vue";
import axios from "axios";
export default {
  name: "app",
  components: {
    RoutingMap,
    ScatterChart,
    Gauge
  },
  data() {
    return {
      waitFor: null,
      data: null,
      items: null
    };
  },
  async created() {
    let createResolve = null;
    this.waitFor = new Promise(function(resolve) {
      createResolve = resolve;
    });
    await axios
      .get(`/static/data.json`)
      .then(response => {
        // JSON responses are automatically parsed.
        this.data = response.data;
        this.items = this.data.items;
      })
      .catch(e => {
        this.errors.push(e);
      });
    createResolve(this.items);
    console.log("created return with.." + JSON.stringify(this.items));
  },
  async mounted() {
   await  this.waitFor.then(r => {
      console.log("mounted waiting for items .." + JSON.stringify(r));
    });
    console.log("mounted return with" + JSON.stringify(this.items));
  },
  methods: {
    navigateTo(item) {
      console.log("Navigate to item..." + item);
      for (var i = 0; i < this.items.length; i++) {
        console.log("Processing..." + JSON.stringify(this.items[i]));
        if (this.items[i].id == item) {
          this.items[i].state = true;
        } else {
          this.items[i].state = false;
        }
      }
    }
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

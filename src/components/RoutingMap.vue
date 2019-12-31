<template>
  <div class="map-container">
    <div id="map" class="map"></div>
  </div>
</template>

<script>
import L from "leaflet";
import "leaflet-routing-machine";
import "leaflet-control-geocoder/dist/Control.Geocoder";

export default {
  name: "RoutingMap",
  props: {
    origin: String,
    destination: String
  },
  data() {
    return {
      data: null,
      errors: []
    };
  },
  created() {
    this.initData();
  },
  methods: {
    initData() {
      var geocoder = new L.Control.Geocoder.nominatim();
      L.Control.geocoder({
        geocoder: geocoder
      });
      (async function(origin, destination) {
        console.log("waiting");
        var points = [];
        var callback = a => {
          points.push(a);
        };
        geocoder.geocode(origin, a => {
          callback(a[0].center);
        });
        geocoder.geocode(destination, a => {
          callback(a[0].center);
        });
        await new Promise(resolve => {
          setTimeout(a => {
            var map = L.map("map");
            L.Routing.control({
              waypoints: points,
              routeWhileDragging: false
            }).addTo(map);

            L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
              attribution: "© OpenStreetMap contributors"
            }).addTo(map);
            console.log("setTimeout executed");
            resolve(a);
          }, 2000);
        });
        console.log("finished execution");
      })(this.origin, this.destination);
      console.log("initData Completed..." + JSON.stringify(this.points));
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.map-container {
  margin: 0 auto;
  max-width: 960px;
}
.map {
  width: 100%;
  height: 640px;
}
</style>

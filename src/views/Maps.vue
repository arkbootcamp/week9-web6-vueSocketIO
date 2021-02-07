<template>
  <div class="maps">
    <h1>Page Maps</h1>
    <div class="container">
      <GmapMap
        :center="coordinate"
        :zoom="10"
        map-type-id="terrain"
        style="width: 500px; height: 300px; margin: auto;"
      >
        <GmapMarker
          :position="coordinate"
          :clickable="true"
          :draggable="true"
          @click="clickMarker"
          icon="https://img.icons8.com/color/48/000000/map-pin.png"
        />
      </GmapMap>
      <br />
      <h3>{{ coordinate }}</h3>
      <h4>accuracy : {{ accuracy }}</h4>
    </div>
  </div>
</template>

<script>
export default {
  name: "Maps",
  data() {
    return {
      coordinate: {
        lat: 10,
        lng: 10
      },
      accuracy: ""
    };
  },
  created() {
    this.$getLocation()
      .then(coordinates => {
        this.coordinate = {
          lat: coordinates.lat,
          lng: coordinates.lng
        };
        this.accuracy = coordinates.accuracy;
        console.log(coordinates);
      })
      .catch(error => {
        alert(error);
      });
  },
  methods: {
    clickMarker(position) {
      console.log("clicked marker");
      console.log(position);
      console.log(position.latLng.lat());
      console.log(position.latLng.lng());
      this.coordinate = {
        lat: position.latLng.lat(),
        lng: position.latLng.lng()
      };
    }
  }
};
</script>

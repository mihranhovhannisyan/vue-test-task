<template>
  <main>
    <select class="city-dropdown" name="cities" v-model="selectedCity">
      <option class="city-dropdown__placeholder" value="" disabled selected>
        Select City
      </option>
      <option v-for="city in cities" :key="city.value" :value="city.value">
        {{ city.name }}
      </option>
    </select>
    <l-map style="height: 600px" :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-marker :lat-lng="markerLatLng"></l-marker>
    </l-map>
  </main>
</template>

<script lang="ts">
import { LMap, LTileLayer, LMarker } from "@vue-leaflet/vue-leaflet";

interface City {
  name: String;
  value: String;
}

export default {
  components: {
    LMap,
    LTileLayer,
    LMarker,
  },

  data() {
    return {
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" as string,
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors' as string,
      zoom: 13 as number,
      center: [51.505, -0.159] as Number[],
      markerLatLng: [51.504, -0.159] as Number[],
      cities: [
        {
          name: "New York",
          value: "new-york",
        },
        {
          name: "Los Angeles",
          value: "los-angeles",
        },
        {
          name: "San Francisco",
          value: "san-francisco",
        },
        {
          name: "Las Vegas",
          value: "las-vegas",
        },
        {
          name: "Austin",
          value: "austin",
        },
      ] as City[],
      selectedCity: "" as string,
    };
  },

  watch: {
    selectedCity(val): void {
      this.axios
        .get(
          `https://nominatim.openstreetmap.org/search?city=${val}&format=json`
        )
        .then((response) => {
          if (response.data.length) {
            const newCoordinates: object[] = [];
            newCoordinates.push(response.data[0].lat);
            newCoordinates.push(response.data[0].lon);
            this.markerLatLng = newCoordinates;
            this.center = newCoordinates;
          }
        });
    },
  },
};
</script>

<style scoped>
.city-dropdown {
  margin-bottom: 20px;
  width: 200px;
  padding: 5px;
  border-radius: 6px;
}

.city-dropdown__placeholder {
  display: none;
}
</style>

<template>
  <div class="home">
    <div>
      <h2>Add Place</h2>
      <ul>
        <li class="error" v-for="error in errors" v-bind:key="error">{{ error }}</li>
      </ul>
      Name:
      <input type="text" v-model="newPlace.name" />
      Address:
      <input type="text" v-model="newPlace.address" />
      <button v-on:click="createPlace()">Add Place</button>
    </div>
    <div v-for="place in places" :key="place.id">
      <h3>Name: {{ place.name }}</h3>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Add Information</h1>
        <p>
          Name:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="updatePlace()">Update</button>
        <button v-on:click="destroyPlace()">Destroy</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      places: [],
      newPlace: {},
      adress: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log("Places", response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("http://localhost:3000/places", this.newPlace)
        .then((response) => {
          console.log("new place added", response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.adress = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function () {
      console.log(this.adress);
      var updatePlaceParams = {
        name: this.adress.name,
        address: this.adress.address,
      };
      axios
        .patch(`http://localhost:3000/places/${this.adress.id}`, updatePlaceParams)
        .then((response) => {
          console.log("updated successfully", response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function () {
      axios.delete(`http://localhost:3000/places/${this.adress.id}`).then((response) => {
        console.log("sucessfully deleted", response.data);
        var index = this.places.indexOf(this.adress);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
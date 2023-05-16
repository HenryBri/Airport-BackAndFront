<template>
  <div>
    <table-template
      caption="All Airports"
      :items="airports"
      :showControls="true"
      @show="airportDetailId = $event.id"
      @delete="airportToDelete = $event"
      @update="airportToUpdate = { ...$event }">
    </table-template>
    <router-link to="/createAirport">Add new Airport</router-link>
  </div>
  <airport-details
    :airportDetailId="airportDetailId"
    @close="airportDetailId = 0"
  ></airport-details>
  <modal :show="JSON.stringify(airportToDelete) !== '{}'">
    <template #header>
      <h3>Delete Airport</h3>
    </template>
    <template #body>
      <p>Are you sure about deleting the airport?</p>
    </template>
    <template #footer>
      <button class="modal-default-button" @click="deleteAirport()">Yes</button>
      <button class="modal-default-button" @click="airportToDelete = {}">
        No
      </button>
    </template>
  </modal>
  <modal :show="airportToUpdate !== null">
    <template #header>
      <h3>Update Airport</h3>
    </template>
    <template #body>
      <form @submit.prevent="updateAirport">
  <div>
    <label>
      Name:
      <input type="text" v-model="airportToUpdate.name" required>
    </label>
  </div>
  <div>
    <label>
      Location:
      <input type="text" v-model="airportToUpdate.location" required>
    </label>
  </div>
  <div>
    <label>
      IATA Code:
      <input type="text" v-model="airportToUpdate.IATA_code" required>
    </label>
  </div>
  <div>
    <label>
      ICAO Code:
      <input type="text" v-model="airportToUpdate.ICAO_code" required>
    </label>
  </div>
  <div>
    <label>
      Info:
      <textarea v-model="airportToUpdate.info" required></textarea>
    </label>
  </div>
  <button class="button">
    Update
  </button>
</form>
    </template>
    <template #footer>
      <button class="button" @click="airportToUpdate = null">
        Cancel
      </button>
    </template>
  </modal>
</template>

<script>
import TableTemplate from "../../components/Table.vue";
import AirportDetails from "../../components/AirportDetails.vue";
import Modal from "../../components/Modal.vue";
import { RouterLink } from "vue-router";
export default {
  components: {
    TableTemplate,
    AirportDetails,
    RouterLink,
    Modal,
  },
  data() {
    return {
      airports: [],
      airportDetailId: 0,
      airportToDelete: {},
      airportToUpdate: null,
    };
  },
  async created() {
    this.airports = await (await fetch("http://localhost:8090/airports/")).json();
  },
  methods: {
    async deleteAirport() {
      fetch("http://localhost:8090/airports/id/" + this.airportToDelete.id, {
        method: "delete",
      }).then(async (response) => {
        if (response.status == 204) {
          this.airports.splice(this.airports.indexOf(this.airportToDelete), 1);
          this.airportToDelete = {};
        } else {
          const data = await response.json();
          console.log("DELETE: ", data);
        }
      });
    },
    updateAirport() {
      fetch(`http://localhost:8090/airports/id/${this.airportToUpdate.id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(this.airportToUpdate)
      }).then(async (response) => {
        if (response.ok) {
            const index = this.airports.findIndex(airport => airport.id === this.airportToUpdate.id);
            this.airports[index].id = this.airportToUpdate.id;
            this.airports[index].name = this.airportToUpdate.name;
            this.airportToUpdate = null;
          } else {
          const data = await response.json();
          console.log('UPDATE: ', data);
        }
      });
    }
  },
};
</script>
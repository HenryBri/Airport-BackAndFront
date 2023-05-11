<template>
  <div>
    <table-template
      caption="All Airports"
      :items="airports"
      :showControls="true"
      @show="airportDetailId = $event.id"
      @delete="airportToDelete = $event">
    </table-template>
    <router-link to="/addAirport">Add new Airport</router-link>
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
    };
  },
  async created() {
    this.airports = await (await fetch("http://localhost:8090/airports/")).json();
  },
  methods: {
    async deleteAirport() {
      fetch("http://localhost:8090/airports/" + this.airportToDelete.id, {
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
  },
};
</script>
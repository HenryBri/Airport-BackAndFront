<template>
  <div>
    <table-template
      caption="All Flights"
      :items="flights"
      :showControls="true"
      @show="flightDetailId = $event.id"
      @delete="flightToDelete = $event"
      @update="flightToUpdate = { ...$event }"
    >
    </table-template>
    <router-link to="/createflight">Add new flight</router-link>
  </div>
  <flight-details
    :flightDetailId="flightDetailId"
    @close="flightDetailId = 0"
  ></flight-details>
  <modal :show="JSON.stringify(flightToDelete) !== '{}'">
    <template #header>
      <h3>Delete Flights</h3>
    </template>
    <template #body>
      <p>Are you sure about deleting the flight?</p>
    </template>
    <template #footer>
      <button class="modal-default-button" @click="deleteFlight()">Yes</button>
      <button class="modal-default-button" @click="flightToDelete = {}">
        No
      </button>
    </template>
  </modal>
  <modal :show="flightToUpdate !== null">
    <template #header>
      <h3>Update Flight</h3>
    </template>
    <template #body>
      <form @submit.prevent="updateFlight">
        <div>
          <label>
            Flight number:
            <input type="text" v-model="flightToUpdate.flight_nr" required>
          </label>
        </div>
        <div>
          <label>
            Departure airport:
            <input type="text" v-model="flightToUpdate.departure_airport" required>
          </label>
        </div>
        <div>
          <label>
            Arrival airport:
            <input type="text" v-model="flightToUpdate.arrival_airport" required>
          </label>
        </div>
        <div>
          <label>
            Info:
            <textarea v-model="flightToUpdate.info" required></textarea>
          </label>
        </div>
        <button class="button">
          Update
        </button>
      </form>
    </template>
    <template #footer>
      <button class="button" @click="flightToUpdate = null">
        Cancel
      </button>
    </template>
  </modal>
</template>
  
<script>
  import TableTemplate from "../../components/Table.vue";
  import FlightDetails from "../../components/FlightDetails.vue";
  import Modal from "../../components/Modal.vue";
  import { RouterLink } from "vue-router";
  export default {
    components: {
      TableTemplate,
      FlightDetails,
      RouterLink,
      Modal,
    },
    data() {
      return {
        flights: [],
        flightDetailId: 0,
        flightToDelete: {},
        flightToUpdate: null,
      };
    },
    async created() {
      this.flights = await (await fetch("http://localhost:8090/flights")).json();
    },
    methods: {
      async deleteFlight() {
        fetch("http://localhost:8090/flights/" + this.flightToDelete.id, {
          method: "delete",
        }).then(async (response) => {
          if (response.status == 204) {
            this.flights.splice(this.flights.indexOf(this.flightToDelete), 1);
            this.flightToDelete = {};
          } else {
            const data = await response.json();
            console.log("DELETE: ", data);
          }
        });
      },
      updateFlight() {
      fetch(`http://localhost:8090/flights/${this.flightToUpdate.id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json'},
          body: JSON.stringify(this.flightToUpdate)
          }).then(async (response) => {
        if (response.ok) {
          const index = this.flights.findIndex(flight => flight.id === this.flightToUpdate.id);
          this.flights.splice(index, 1, this.flightToUpdate);
          this.flightToUpdate = null;
        } else {
          const data = await response.json();
          console.log('UPDATE: ', data);
        }
     });
   },
},
};
</script>
  
  <style scoped>
  header {
    line-height: 1.5;
    max-height: 100vh;
  }
  
  .logo {
    display: block;
    margin: 0 auto 2rem;
  }
  
  nav {
    width: 100%;
    font-size: 12px;
    text-align: center;
    margin-top: 2rem;
  }
  
  nav a.router-link-exact-active {
    color: var(--color-text);
  }
  
  nav a.router-link-exact-active:hover {
    background-color: transparent;
  }
  
  nav a {
    display: inline-block;
    padding: 0 1rem;
    border-left: 1px solid var(--color-border);
  }
  
  nav a:first-of-type {
    border: 0;
  }
  
  @media (min-width: 1024px) {
    header {
      display: flex;
      place-items: center;
      padding-right: calc(var(--section-gap) / 2);
    }
  
    .logo {
      margin: 0 2rem 0 0;
    }
  
    header .wrapper {
      display: flex;
      place-items: flex-start;
      flex-wrap: wrap;
    }
  
    nav {
      text-align: left;
      margin-left: -1rem;
      font-size: 1rem;
  
      padding: 1rem 0;
      margin-top: 1rem;
    }
  }
  </style>
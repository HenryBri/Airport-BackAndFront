<template>
  <div>
    <table-template
      caption="All Airports"
      :items="airports"
      :showControls="true"
      @show="airportDetailId = $event.id"
      @delete="airportToDelete = $event">
    </table-template>
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

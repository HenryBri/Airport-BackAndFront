<template>
    <div>
      <table-template
        caption="All Flights"
        :items="flights"
        :showControls="true"
        @show="flightDetailId = $event.id"
        @delete="flightToDelete = $event"
      >
      </table-template>
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
        <p>Are you sure about deleting the airport?</p>
      </template>
      <template #footer>
        <button class="modal-default-button" @click="deleteFlight()">Yes</button>
        <button class="modal-default-button" @click="flightToDelete = {}">
          No
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

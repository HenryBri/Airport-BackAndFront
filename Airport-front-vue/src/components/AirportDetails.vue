<template>
    <Teleport to="body">
      <!-- use the modal component, pass in the prop -->
      <modal :show="airportDetailId != 0" @close="$emit('close')">
        <template #header>
          <h3>Airport Details</h3>
        </template>
        <template #body>
            <b>Name: </b>{{ currentAirport.name }}<br />
            <b>Location: </b>{{ currentAirport.location }}<br />
            <b>IATA code: </b>{{ currentAirport.IATA_code }}<br />
            <b>ICAO code: </b>{{ currentAirport.ICAO_code }}<br />
            <b>Info: </b>{{ currentAirport.info }}<br />
        </template>
      </modal>
    </Teleport>
  </template>
  <script>
  import Modal from "./Modal.vue";
  export default {
    components: {
      Modal,
    },
    props: {
        airportDetailId: {
        type: Number,
        required: true,
      },
    },
    emits: ["close"],
    data() {
      return {
        currentAirport: {
            id: 0,
            name: "",
            location: "",
            IATA_code: "",
            ICAO_code: "",
            info: "",
        },
      };
    },
    beforeUpdate() {
      if (this.airportDetailId == 0) return;
      this.getDetails();
    },
    methods: {
      async getDetails() {
        this.currentAirport = await (
          await fetch(`http://localhost:8090/airports/id/${this.airportDetailId}`)
        ).json();
      },
    },
  };
  </script>
  <style>
  .modal-container {
    width: 700px;
  }
  </style>
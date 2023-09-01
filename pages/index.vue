<template>
  <div>
    <create-event v-if="eventModal" v-model="eventModal" @close="eventModal = false"></create-event>
    <v-btn @click="eventModal = true" color="primary">Create An Event</v-btn>
    <event-table :items="events"></event-table>
  </div>
</template>

<script>

import CreateEvent from '../components/CreateEvent.vue'
export default {
  name: 'IndexPage',
  components: {
    CreateEvent
  },
  data() {
    return {
      eventModal: false,
      events: []
    }
  },
  mounted() {
    //would replace for axios call from the api layer
    this.events = JSON.parse(window.localStorage.getItem('events'))
  }
}
</script>

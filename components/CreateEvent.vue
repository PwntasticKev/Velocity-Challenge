<template>
  <v-dialog value="value" max-width="500px" persistent>
  <v-form @submit="calculateEventDate" ref="eventForm">
  <v-card>
    <v-card-title>
      <v-row class="justify-space-between">
        <v-col>
          Modal Title
        </v-col>
          <v-btn class="ma-2" small @click="$emit('close')">
            <v-icon size="20">close</v-icon>
          </v-btn>
      </v-row>
    </v-card-title>
    <v-card-text>
    <v-text-field
      label="Event Name"
      :rules="rules"
      v-model="event.eventName"
      hide-details="auto"
    ></v-text-field>

    <v-text-field
      label="Organizer"
      :rules="rules"
      v-model="event.organizerName"
      hide-details="auto"
    ></v-text-field>
    </v-card-text>
    <v-divider></v-divider>


    <v-card-title>
      <v-icon size="13">mdi-event</v-icon>
      Date & Times
    </v-card-title>
    <v-card-text class="pb-0">
      <v-row class="justify-space-between">
        <v-col>
      <v-select v-model="event.selectedType" :items="selectType" label="Time Option" @change="event.timeslots = []"></v-select>
          <v-select v-model="event.selectedRepeat" :items="minuteIncrements" label="Repeat"></v-select>
            <v-select v-model="event.selectedOccurance" :items="timeIncrements" label="Time Occurance"></v-select>
          <v-select v-model="event.selectedFrequency" :items="frequencies" label="Select Frequency"></v-select>
          <v-select
            v-if="event.selectedFrequency === 'weekly'"
            :items="dayOptions"
            :menu-props="{ maxHeight: '400' }"
            label="Select"
            multiple
            hint="Choose Days for repeating"
            persistent-hint
          ></v-select>
<!--          // add multiple different time slots-->
          <div v-if="event.selectedType === 'Specific Times'">
            <v-btn @click="addTimeSlot" >Add Time</v-btn>
            <v-row v-for="(time, i) in event.timeSlots" :key="i" class="justify-space-between align-center">
              <v-col>
                <v-datetime-picker label="Select Start Time" v-model="time.startTime"></v-datetime-picker>
              </v-col>
              <v-col>
                <v-datetime-picker label="Select End Time" v-model="time.endTime"></v-datetime-picker>
              </v-col>
             <v-btn @click="event.timeSlots.splice(1,i)" icon outlined>
              <v-icon size="20">close</v-icon>
             </v-btn>
            </v-row>
          </div>

          <div v-else>
            <v-row class="justify-space-between align-center">
              <v-col>
                <v-datetime-picker label="Select Start Time" v-model="event.startTime"></v-datetime-picker>
              </v-col>
              <v-col>
                <v-datetime-picker label="Select End Time" v-model="event.endTime"></v-datetime-picker>
              </v-col>

            </v-row>
          </div>
        </v-col>

      </v-row>
    </v-card-text>

    <v-card-actions>
      <v-btn @click="calculateEventDate" class="ma-2" color="primary" :disabled="isFormValid">Create Event</v-btn>
    </v-card-actions>
      </v-card>
    </v-form>
  </v-dialog>
</template>

<script>
export default {
  props: {
    value: {
      type: Boolean,
      default: () => false
    }
  },
  data() {
    return {
        rules: [
          value => !!value || 'Required.',
          value => (value && value.length >= 3) || 'Min 3 characters',
        ],
      selectType: ['Time Increments', 'Specific Times'],
      frequencies: ['never', 'daily', 'weekly', 'monthly', 'yearly'],
      timeIncrements: ['15 minutes', '30 minutes', '1 hour'],
      dayOptions: ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday'],
      minuteIncrements: ['5', '10', '15', '30'],
      endDateTimePicker: false,
      event: {
        timeSlots: [],
        daysSelected: [],
        eventName: '',
        organizerName: '',
        selectedType: 'Time Increments',
        selectedFrequency: '',
        selectedOccurance: '',
        selectedRepeat: '',
        startDateTime: '',
        endTime: '',
        startTime: '',
        startDateTimePicker: '',
        endDate: '',
        eventDate: null,
      }
    };
  },
  computed: {
    calculatedEventDate() {
      // Add your calculations here using the selected values and return the event date
      return this.eventDate;
    },
    isFormValid() {
      if (this.$refs.eventForm) {
        return this.$refs.eventForm.validate();
      }
      return false;
    },
  },
  methods: {
    calculateEventDate() {
      const events = JSON.parse(window.localStorage.getItem('events')) || []

      events.push(this.event)
      // add to local storage of the event created
      window.localStorage.setItem('events', JSON.stringify(events))
      this.eventDate = "Calculated Event Date";
    },

    addTimeSlot() {
      this.event.timeSlots.push(
        {
          startTime: '',
          endTime: ''
        }
      )
    }
  }
};
</script>

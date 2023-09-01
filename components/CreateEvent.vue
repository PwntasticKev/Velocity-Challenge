<template>
  <v-dialog value="value" max-width="500px" persistent>
    <v-form @submit="calculateEventDate" ref="eventForm">
      <v-card class="pa-4">
        <v-card-title>
          <v-row class="justify-space-between">
            <v-col>
              Create an Event
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
              <v-select v-model="event.selectedOccurence" :items="timeIncrements" label="Time Occurance"></v-select>
              <v-select v-model="event.selectedFrequency" :items="frequencies" label="Select Frequency"></v-select>
              <v-select
                v-if="event.selectedFrequency === 'weekly'"
                :items="dayOptions"
                v-model="event.daysSelected"
                :menu-props="{ maxHeight: '400' }"
                label="Select"
                multiple
                hint="Choose Days for repeating"
                persistent-hint
              ></v-select>
              <!--   add multiple different time slots-->
              <div v-if="event.selectedType === 'Specific Times'">
                <v-btn @click="addTimeSlot" >Add Time</v-btn>
                <v-row v-for="(time, i) in event.timeSlots" :key="i" class="justify-space-between align-center">
                  <v-col>
                    <v-datetime-picker label="Select Start Time" v-model="time.startTime"></v-datetime-picker>
                  </v-col>
                  <v-col>
                    <v-datetime-picker label="Select End Time" v-model="time.endTime"></v-datetime-picker>
                  </v-col>
                 <v-btn small @click="event.timeSlots.splice(i,1)" icon outlined>
                  <v-icon size="16">close</v-icon>
                 </v-btn>
                </v-row>
              </div>
              <!--//otherwise just show one and reset the others-->
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
        <v-card-text v-if="!!event.startTime">{{calculatedEventDate}}</v-card-text>
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
      dayOptions: ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday'],
      endDateTimePicker: false,
      event: {
        timeSlots: [],
        daysSelected: [],
        eventName: '',
        organizerName: '',
        selectedType: 'Time Increments',
        selectedFrequency: 'daily',
        selectedOccurence: '',
        selectedRepeat: '',
        startDateTime: '',
        endTime: '',
        startTime: '',
      },
      frequencies: ['never', 'daily', 'weekly', 'monthly', 'yearly'],
      minuteIncrements: ['5', '10', '15', '30'],
      rules: [
        value => !!value || 'Required.',
        value => (value && value.length >= 3) || 'Min 3 characters',
      ],
      selectType: ['Time Increments', 'Specific Times'],
      timeIncrements: ['15 minutes', '30 minutes', '1 hour'],
    };
  },
  computed: {
    calculatedEventDate() {
      return `${this.event.selectedFrequency} starting on ${this.event.startTime} and Will end on ${this.event.endTime}`
    },
    isFormValid() {
      if (this.$refs.eventForm) {
        return this.$refs.eventForm.validate();
      }
      return false;
    },
  },
  methods: {
    addTimeSlot() {
      this.event.timeSlots.unshift(
        {
          startTime: '',
          endTime: ''
        }
      )
    },

    calculateEventDate() {
      const events = JSON.parse(window.localStorage.getItem('events')) || []

      events.push(this.event)
      // add to local storage of the event created
      window.localStorage.setItem('events', JSON.stringify(events))

      this.$emit('close')
    },

  }
};
</script>

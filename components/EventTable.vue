<template>
  <v-simple-table>
    <template v-slot:default>
      <thead>
      <tr>
        <th v-for="title in tableHeaderTitles" :key="title">
          {{title}}
        </th>
      </tr>
      </thead>
      <tbody>
      <tr
        v-for="item in items"
        :key="item.name"
      >
        <td>{{ item.eventName }}</td>
        <td>{{ item.organizerName }}</td>
        <td>{{ item.selectedFrequency }}</td>
        <td>{{ item.selectedOccurence }}</td>
        <td>{{ item.selectedRepeat }}</td>
        <td>{{ item.daysSelected }}</td>
<!--        <td>-->
<!--          {{item.timeslots}}-->
<!--        </td>-->
        <td v-for="(timeslot, idx) in item.timeslots" :key="idx">
          {{timeslot.startTime}} | {{timeslot.endTime}}
        </td>
      </tr>
      </tbody>
    </template>
  </v-simple-table>
</template>

<script>
export default {
  props: {
    items: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      tableHeaderTitles: ['Name', 'Organizer', 'Frequency', 'Occurrence', 'Repeat Amnt', 'Days', 'Time Slots']
    }
  }
}

</script>

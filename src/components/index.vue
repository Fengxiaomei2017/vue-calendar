<template>
  <Calendar :single=false class='calendar'></Calendar>
</template>
<script>
import Calendar from '@/components/calendar.vue'
export default {
  components: {
    Calendar
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .calendar {
      position: fixed;
      top: 0;
      left: 0;
    }
</style>

<template>
  <div>
    <div class="buttons">
      <button @click="$refs.calendar.toPre()">pre</button>
      <button @click="$refs.calendar.toNext()">next</button>
      <button @click="$refs.calendar.toToday()">toToday</button>
      <button @click="$refs.calendar.changeMode()">changeMode</button>
    </div>
    <calendar ref="calendar"></calendar>
  </div>
</template>

<script>
import Calendar from "../../package/calendar/calendar.vue";

export default {
  components: {
    Calendar,
  },
  data() {
    return {};
  },
};
</script>

<style lang="less" scoped>
.buttons {
  margin: 0 0 20px 0;
}
</style>
<template>
  <div>
    <calendar :disabledDates="disabledDates"></calendar>
    <calendar
      :disDateStrategy="disDateStrategy"
      :disabledDates="disabledDatesFun"
    ></calendar>
  </div>
</template>

<script>
import Calendar from "../../package/calendar/calendar.vue";

export default {
  components: {
    Calendar,
  },
  data() {
    let date = new Date();
    return {
      disabledDates: [
        new Date(date.getFullYear(), date.getMonth(), 5),
        new Date(date.getFullYear(), date.getMonth(), 15),
        new Date(date.getFullYear(), date.getMonth(), 20),
      ],
    };
  },
  methods: {
    disabledDatesFun(date) {
      return date.getDate() % 2 == 0;
    },
    disDateStrategy() {
      return new Date();
    },
  },
};
</script>

<style lang="less" scoped>
/deep/ .disabledDate {
  background: gray;
}
</style>
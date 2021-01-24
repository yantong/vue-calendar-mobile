<template>
  <div>
    <calendar
      @change="change"
      @slideEnd="slideEnd"
      @click="click"
      @touchStart="touchStart"
      @touchMove="touchMove"
      @touchEnd="touchEnd"
    ></calendar>
  </div>
</template>

<script>
import Calendar from "../../package/calendar/calendar.vue";

export default {
  components: {
    Calendar,
  },
  methods: {
    change(e) {
      console.log("change ", e);
    },
    slideEnd(e) {
      console.log("slideEnd ", e);
    },
    click(e) {
      console.log("click ", e);
    },
    touchStart(e) {
      console.log("touchStart ", e);
    },
    touchMove(e) {
      console.log("touchMove ", e);
    },
    touchEnd(e) {
      console.log("touchEnd ", e);
    },
  },
};
</script>

<style lang="less" scoped>
</style>
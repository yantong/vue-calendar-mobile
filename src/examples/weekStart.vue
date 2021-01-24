<template>
  <div>
    <div class="firstDayOfWeek">
      <button @click="decress">减少一天</button>
      <div>周起始日: 星期{{ weekText[firstDayOfWeek % 7] }}</div>
      <button @click="firstDayOfWeek++">增加一天</button>
    </div>
    <calendar :firstDayOfWeek="firstDayOfWeek" v-model="date"></calendar>
  </div>
</template>

<script>
import Calendar from "../../package/calendar/calendar.vue";

export default {
  components: {
    Calendar,
  },
  data() {
    return {
      date: new Date(),
      firstDayOfWeek: 0,

      weekText: ["日", "一", "二", "三", "四", "五", "六"],
    };
  },
  methods: {
    decress() {
      this.firstDayOfWeek--;

      if (this.firstDayOfWeek < 0)
        this.firstDayOfWeek = this.firstDayOfWeek + 7;
    },
  },
};
</script>

<style lang="less" scoped>
.firstDayOfWeek {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;

  button {
    margin: 0 20px;
  }
}
</style>
<template>
  <div>
    <div class="date">
      日期：
      {{
        date.getFullYear() + "/" + (date.getMonth() + 1) + "/" + date.getDate()
      }}
    </div>
    <calendar v-model="date"></calendar>
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
    };
  },
};
</script>

<style lang="less" scoped>
.date {
  margin: 0 0 20px 0;
}
</style>
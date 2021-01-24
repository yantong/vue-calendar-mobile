<template>
  <div>
    <div class="date">
      最小日期：
      {{
        minDate.getFullYear() +
        "/" +
        (minDate.getMonth() + 1) +
        "/" +
        minDate.getDate()
      }}
    </div>
    <div class="date">
      最大日期：
      {{
        maxDate.getFullYear() +
        "/" +
        (maxDate.getMonth() + 1) +
        "/" +
        maxDate.getDate()
      }}
    </div>
    <calendar :minDate="minDate" :maxDate="maxDate" v-model="date"></calendar>
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
      date: date,

      minDate: new Date(date.getFullYear(), date.getMonth() - 1, 15),
      maxDate: new Date(date.getFullYear(), date.getMonth() + 1, 15),
    };
  },
};
</script>

<style lang="less" scoped>
.date {
  margin: 0 0 20px 0;
}

/deep/ .disabledDate {
  background: gray;
}
</style>
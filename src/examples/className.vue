<template>
  <div>
    <calendar
      dateClassName="dateClassName"
      disDateClassName="disDateClassName"
      todayClassName="todayClassName"
      curDateClassName="curDateClassName"
      notCurMonDateClassName="notCurMonDateClassName"
      :disabledDates="disabledDates"
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
};
</script>

<style lang="less" scoped>
/deep/ .dateClassName {
  background: red;
}

/deep/ .disDateClassName {
  background: green;
}

/deep/ .todayClassName {
  background: blue;
}

/deep/ .curDateClassName {
  background: orange;
}

/deep/ .notCurMonDateClassName {
  background: purple;
}
</style>
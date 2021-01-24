<template>
  <div>
    <calendar
      v-model="date"
      @touchStart="touchStart"
      @touchMove="touchMove"
      @touchEnd="touchEnd"
      @slideEnd="slideEnd"
    >
      <template #header>
        <div class="head">
          {{
            date.getFullYear() +
            "/" +
            (date.getMonth() + 1) +
            "/" +
            date.getDate()
          }}
        </div>
      </template>
      <template #weekday>
        <div class="week-days" v-for="(item, index) in weekText" :key="index">
          <div class="day">
            {{ item }}
          </div>
        </div>
      </template>
      <template #day="dateItem">
        <div class="dates">
          <div>{{ dateItem.date.getDate() }}</div>
          <div>{{ dateItem.dateType }}</div>
        </div>
      </template>
      <template #footer>
        <div class="footer">
          <div v-if="!isEnd && touch.touches[0]">
            X: {{ touch.touches[0].clientX }} <br />
            Y：{{ touch.touches[0].clientY }}
          </div>
          <div v-if="!isEnd && !touch.touches[0]">touchEnd</div>
          <div v-if="isEnd">滑动结束</div>
        </div>
      </template>
    </calendar>
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
      weekText: ["Sun", "Mon", "Tue", "Wed", "Thur", "Fri", "Sat"],

      touch: {},
      isEnd: true,
    };
  },
  methods: {
    touchStart(e) {
      this.touch = e;
      this.isEnd = false;
    },
    touchMove(e) {
      this.touch = e;
    },
    touchEnd(e) {
      this.touch = e;
    },
    slideEnd() {
      this.isEnd = true;
    },
  },
};
</script>

<style lang="less" scoped>
.head,
.footer {
  text-align: center;
}

.dates {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.footer {
  margin-top: 20px;
}
</style>
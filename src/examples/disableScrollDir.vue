<template>
  <div>
    <div class="radios">
      <div>
        <input type="radio" v-model="disabledScrollDir" value="right" /> right
      </div>
      <div>
        <input type="radio" v-model="disabledScrollDir" value="left" /> left
      </div>
      <div>
        <input type="radio" v-model="disabledScrollDir" value="up" /> up
      </div>
      <div>
        <input type="radio" v-model="disabledScrollDir" value="down" /> down
      </div>
      <div>
        <input
          type="radio"
          v-model="disabledScrollDir"
          :value="['left', 'right']"
        />
        left, right
      </div>

      <div>
        <input
          type="radio"
          v-model="disabledScrollDir"
          :value="['up', 'down']"
        />
        up, down
      </div>
    </div>
    <calendar :disabledScrollDir="disabledScrollDir" v-model="date"></calendar>
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
      disabledScrollDir: "",
    };
  },
};
</script>

<style lang="less" scoped>
.radios {
  margin-bottom: 20px;
}
</style>
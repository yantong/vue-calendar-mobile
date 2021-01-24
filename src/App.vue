<template>
  <div>
    <div>
      <select v-model="page">
        <option
          v-for="(item, index) in Object.keys(pages)"
          :key="index"
          :value="item"
        >
          {{ item }}
        </option>
      </select>
    </div>
    <div class="component">
      <component :is="pages[page]" />
    </div>
  </div>
</template>

<script>
import baseCom from "./examples/base.vue";
import slotCom from "./examples/slot.vue";
import weekStart from "./examples/weekStart.vue";
import min_max from "./examples/min&max.vue";
import disableDate from "./examples/disableDates.vue";
import disableScrollDir from "./examples/disableScrollDir.vue";
import className from "./examples/className.vue";
import fun from "./examples/fun.vue";
import event from "./examples/event.vue";

export default {
  name: "App",
  data() {
    return {
      pages: {
        基础功能: baseCom,
        自定义slot: slotCom,
        周起始日: weekStart,
        最大最小日期: min_max,
        禁用日期: disableDate,
        禁用滑动方向: disableScrollDir,
        自定义class: className,
        暴露方法: fun,
        事件: event,
      },

      page: "基础功能",
    };
  },
};
</script>

<style lang="less" scoped>
body {
  margin: 0;
  padding: 0;

  #app {
    padding: 5px;
  }
}

.component {
  margin-top: 20px;
}
</style>

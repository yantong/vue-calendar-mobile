<template>
  <div
    class="vue-mobile-calendar"
    @touchstart="touchStart"
    @touchmove.stop.prevent="touchMove"
    @touchend="touchEnd"
    :style="{
      'pointer-events': !isChangeMode ? '' : 'none',
    }"
  >
    <div class="head">
      <slot name="header">
        <div class="head-content">
          <img src="../img/left.png" @click="toMonth('pre')" />
          <div class="head-text">
            {{ dateText }}
          </div>
          <img src="../img/right.png" @click="toMonth('next')" />
        </div>
      </slot>
    </div>
    <div class="week-text">
      <slot name="weekday">
        <div
          class="week-days"
          v-for="(item, index) in showWeekText"
          :key="index"
        >
          <div
            :style="{ color: index == 0 || index == 6 ? '#A6A9AD' : '#323538' }"
            class="day"
          >
            {{ item }}
          </div>
        </div>
      </slot>
    </div>
    <div
      ref="calendarcon"
      class="calendar-contanier"
      :style="{
        transitionDuration: `${isTouching ? 0 : transitionDuration}s`,
      }"
    >
      <div
        ref="calendar"
        class="dates-list"
        :style="{
          transform: `translate3d(${-translateIndex * 100}%, 0, 0)`,
          height: (isShowWeek ? height / 6 : height) + 'px',
        }"
      >
        <div
          class="dates-contanier"
          :class="{ 'month-mode': !isShowWeek }"
          v-for="(dates, index) in isShowWeek ? showDatesWeek : showDates"
          :key="index"
          :style="{
            transform: `translate3d(${
              (translateIndex - 1 + (isTouching ? touch.x : 0)) * 100
            }%,${rowTranslateY},0)`,
            transitionDuration: `${
              isTouching || disAnimation ? 0 : transitionDuration
            }s`,
          }"
        >
          <div
            class="dates-row"
            v-for="(dateRow, rowIndex) in getDatesRow(dates)"
            :key="rowIndex"
          >
            <div
              class="date-item"
              v-for="(item, dateIndex) in dateRow"
              :key="dateIndex"
              :class="{
                'cur-date': isCurDate(item) && !isHideDate(item),
                today: isToday(item.date) && !isHideDate(item),
                disabledDate: isDisableDate(item.date) && !isHideDate(item),
                ['middle-date-' + id]: index == 1,
                ...customClassName(item),
              }"
              @click="selDate(item)"
            >
              <slot
                name="day"
                :dateType="item.dateType"
                :date="item.date"
                v-if="!isHideDate(item)"
              >
                <div
                  class="date"
                  :class="{ notCurMon: item.dateType != 'cur' }"
                >
                  {{ item.date.getDate() }}
                </div>
              </slot>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="footer">
      <slot name="footer">
        <div class="footer-content">
          <img :src="slideStatusImg" />
        </div>
      </slot>
    </div>
  </div>
</template>

<script>
import "./calendar.less";

let id = 0;

export default {
  name: "VueCalenderMobile",
  props: {
    value: {
      type: Date,
      default() {
        return new Date();
      },
    },
    hideNotCurMonDay: {
      type: Boolean,
      default: false,
    },
    defaultWeekMode: {
      type: Boolean,
      default: false,
    },
    transitionDuration: {
      type: Number,
      default: 0.3,
    },
    firstDayOfWeek: {
      type: Number,
      default: 7,
    },
    maxDate: {
      type: Date,
      default: null,
    },
    minDate: {
      type: Date,
      default: null,
    },
    disabledDates: {
      type: [Array, Function],
      default() {
        return [];
      },
    },
    disDateStrategy: {
      type: Function,
      default: null,
    },
    disabledScrollDir: {
      type: [Array, String],
      default: "",
    },
    dateClassName: {
      type: String,
      default: "",
    },
    disDateClassName: {
      type: String,
      default: "",
    },
    todayClassName: {
      type: String,
      default: "",
    },
    curDateClassName: {
      type: String,
      default: "",
    },
    notCurMonDateClassName: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      currentDate: null,
      today: new Date(),

      translateIndex: 0,
      isTouching: false,
      touch: {
        x: 0,
        y: 0,
      },
      touchStartPositionX: null,
      touchStartPositionY: null,
      rowTranslateY: 0,

      //isWeekMode表示当前滑动切换还未完成前的模式，如果没有完成月和周的切换，继续保持之前的模式
      //isShowWeek表示当前正在显示的模式
      isWeekMode: false,
      isShowWeek: false,

      disAnimation: false,
      isChangeMode: false,
      changeModeTimer: null,

      scrollingDir: "",
      disScrollDir: "",

      weekText: ["日", "一", "二", "三", "四", "五", "六"],

      height: 300,

      id: id++,

      downImg: require("../img/slide_down.png"),
      upImg: require("../img/slide_up.png"),
      defImg: require("../img/slide_def.png"),
      slideStatus: "def",
    };
  },
  created() {
    this.isShowWeek = this.isWeekMode = this.defaultWeekMode;
    this.disScrollDir = this.disabledScrollDir;
    this.currentDate = this.value || this.today;

    //通过找到子元素中最高的元素来设置日历的高度
    this.$nextTick(() => {
      let middleDates = document.querySelectorAll(
        `.middle-date-${this.id} > *`
      );
      let height = [].map.call(middleDates, (item) => {
        return item.clientHeight;
      });

      height.sort().reverse();
      this.height = height[0] * (this.showDates[1].length / 7);
    });
  },
  watch: {
    touch(newValue, old) {
      if (newValue.y == 0) {
        this.slideStatus = "def";
      } else {
        this.slideStatus = newValue.y > old.y ? "down" : "up";
      }
    },
  },
  computed: {
    dateText() {
      if (!this.currentDate) return "";

      let year = this.currentDate.getFullYear();
      let month = this.currentDate.getMonth();

      return year + "年" + (month + 1) + "月";
    },
    slideStatusImg() {
      let imgMap = {
        def: this.defImg,
        up: this.upImg,
        down: this.downImg,
      };

      return imgMap[this.slideStatus];
    },
    showWeekText() {
      let weekText = [...this.weekText, ...this.weekText];
      let start = this.firstDayOfWeek % 7;

      return weekText.slice(start, start + 7);
    },

    showDates() {
      let year = this.currentDate.getFullYear();
      let month = this.currentDate.getMonth();
      let last = this.getLastYearMon(year, month);
      let next = this.getNextYearMon(year, month);
      let showDates = [
        this.getFullDates(last.year, last.month),
        this.getFullDates(year, month),
        this.getFullDates(next.year, next.month),
      ];

      return showDates;
    },
    showDatesWeek() {
      let year = this.currentDate.getFullYear();
      let month = this.currentDate.getMonth();
      let last = this.getLastYearMon(year, month);
      let next = this.getNextYearMon(year, month);
      let showDates = [
        this.getFullDates(last.year, last.month),
        this.getFullDates(year, month),
        this.getFullDates(next.year, next.month),
      ];
      let dateRows = [
        this.getDatesRow(showDates[0]),
        this.getDatesRow(showDates[1]),
        this.getDatesRow(showDates[2]),
      ];
      let curIndex = dateRows[1].findIndex((items) =>
        items.some((item) => this.isCurDate(item))
      );
      let showDatesWeek = [];

      if (curIndex >= 1 && curIndex <= dateRows[1].length - 2) {
        showDatesWeek[0] = dateRows[1][curIndex - 1];
        showDatesWeek[1] = dateRows[1][curIndex];
        showDatesWeek[2] = dateRows[1][curIndex + 1];
      } else if (curIndex < 1) {
        showDatesWeek[0] = dateRows[0][dateRows[0].length - 1];
        showDatesWeek[1] = dateRows[1][curIndex];
        showDatesWeek[2] = dateRows[1][curIndex + 1];
      } else if (curIndex > dateRows[1].length - 2) {
        showDatesWeek[0] = dateRows[1][curIndex - 1];
        showDatesWeek[1] = dateRows[1][curIndex];
        showDatesWeek[2] = dateRows[2][0];
      }

      return showDatesWeek;
    },
  },
  methods: {
    isSameDate(date, date2) {
      return (
        date.getFullYear() == date2.getFullYear() &&
        date.getMonth() == date2.getMonth() &&
        date.getDate() == date2.getDate()
      );
    },
    isCurDate(item) {
      return (
        this.isSameDate(item.date, this.currentDate) && item.dateType == "cur"
      );
    },
    isToday(date) {
      return this.isSameDate(date, this.today);
    },
    isHideDate(item) {
      return this.hideNotCurMonDay && item.dateType != "cur";
    },

    isDisableDate(date) {
      if (typeof this.disabledDates == "function") {
        return this.disabledDates(date);
      } else if (this.disabledDates && Array.isArray(this.disabledDates)) {
        if (
          this.disabledDates.some((item) => {
            return this.isSameDate(date, item);
          })
        )
          return true;
      }

      if (this.maxDate && date.getTime() > this.maxDate.getTime()) return true;
      if (this.minDate && date.getTime() < this.minDate.getTime()) return true;
    },
    isDisabledRow(dates, leftMove) {
      let date = dates[leftMove ? dates.length - 1 : 0].date;

      if (this.maxDate && date.getTime() > this.maxDate.getTime()) return true;
      if (this.minDate && date.getTime() < this.minDate.getTime()) return true;
    },
    validateDate(date, type) {
      //设置完日期后需要对日期进行验证，如果当前日期是disabled的就需要进行调整
      //默认是往左滑找到数组中index最小的可用的日期，往右滑找到数组中index最大的可用的日期
      //如果上一级数组或下一级数组中找不到可用的日期会继续找上一级或下一级数组直到找到为止
      //如果设置了disDateStrategy则按disDateStrategy的返回日期

      if (!this.isDisableDate(date)) return (this.currentDate = date);

      if (this.disDateStrategy && typeof this.disDateStrategy == "function") {
        this.currentDate = this.disDateStrategy(date);

        return;
      }

      let dates = (this.isWeekMode ? this.showDatesWeek : this.showDates)[
        type == "pre" ? 0 : 2
      ];
      if (type == "next") dates = dates.reverse();
      let index = dates.findIndex(
        (item) =>
          !this.isDisableDate(item.date) &&
          !this.isHideDate(item) &&
          item.dateType == "cur"
      );

      if (index < 0 || this.isSameDate(this.currentDate, dates[index].date)) {
        let always = true;
        while (always) {
          let time =
            date.getTime() + 24 * 60 * 60 * 1000 * (type == "pre" ? -1 : 1);

          if (time < 0) break;

          date.setTime(time);
          if (!this.isDisableDate(date)) {
            this.currentDate = date;

            break;
          }
        }
      } else this.currentDate = dates[index].date;
    },

    customClassName(item) {
      let classNames = {};

      if (this.dateClassName) classNames[this.dateClassName] = true;

      if (
        this.disDateClassName &&
        this.isDisableDate(item.date) &&
        !this.isHideDate(item)
      )
        classNames[this.disDateClassName] = true;

      if (
        this.todayClassName &&
        this.isToday(item.date) &&
        !this.isHideDate(item)
      )
        classNames[this.todayClassName] = true;

      if (
        this.curDateClassName &&
        this.isCurDate(item) &&
        !this.isHideDate(item)
      )
        classNames[this.curDateClassName] = true;

      if (
        this.notCurMonDateClassName &&
        item.dateType != "cur" &&
        !this.isHideDate(item)
      )
        classNames[this.notCurMonDateClassName] = true;

      return classNames;
    },

    getLastYearMon(year, month) {
      return {
        year: year - (month == 0 ? 1 : 0),
        month: month == 0 ? 11 : month - 1,
      };
    },
    getNextYearMon(year, month) {
      return {
        year: year + (month == 11 ? 1 : 0),
        month: month == 11 ? 0 : month + 1,
      };
    },
    getMonthFirstDay(year, month) {
      let date = new Date();
      date.setFullYear(year, month, 1);

      return date.getDay();
    },
    getMonthLastDay(year, month) {
      let date = new Date();
      date.setFullYear(year, month, this.getMonthDays(year, month));

      return date.getDay();
    },
    getMonthDays(year, month) {
      let days = new Date(year, month + 1, 0).getDate();
      return days;
    },
    getFullDates(year, month) {
      let last = this.getLastYearMon(year, month);
      let next = this.getNextYearMon(year, month);

      let days = this.getMonthDays(year, month);
      let last_days = this.getMonthDays(last.year, last.month);

      let first_day = this.getMonthFirstDay(year, month);
      let last_day = this.getMonthLastDay(year, month);

      let dates = [];

      let start = this.firstDayOfWeek % 7;
      let preIndex = (first_day - start + 7) % 7;
      let nextIndex = (6 - last_day + start) % 7;

      for (let index = 0; index < days; index++) {
        let date = new Date();
        date.setFullYear(year, month, index + 1);
        dates.push({
          dateType: "cur",
          date,
        });
      }

      if (days == 28 && first_day == 0) {
        return dates;
      } else {
        for (let index = 0; index < preIndex; index++) {
          let date = new Date();
          date.setFullYear(last.year, last.month, last_days - index);
          dates.unshift({
            dateType: "pre",
            date,
          });
        }

        if (last_day != 6)
          for (let index = 0; index < nextIndex; index++) {
            let date = new Date();
            date.setFullYear(next.year, next.month, index + 1);
            dates.push({
              dateType: "next",
              date,
            });
          }
      }

      return dates;
    },

    getDatesRow(dates) {
      var result = [];
      for (var i = 0, len = dates.length; i < len; i += 7) {
        result.push(dates.slice(i, i + 7));
      }

      return result;
    },
    getCurDatesOffsetY() {
      //选中日期所在行的高度

      let curIndex = this.showDates[1].findIndex((item) =>
        this.isCurDate(item)
      );
      curIndex = parseInt(curIndex / 7);
      let length = this.showDates[1].length / 7;
      let height = this.height / 6;
      let margin = length == 5 ? (height / 4.0) * curIndex : 0;
      let offset = height * curIndex + margin;

      return Math.ceil(offset);
    },

    disableScroll(isX, move) {
      if (isX) {
        if (
          this.isDisabledRow(
            this.isWeekMode
              ? this.showDatesWeek[move > 0 ? 0 : 2]
              : this.showDates[move > 0 ? 0 : 2],
            move > 0
          )
        )
          return true;
      }

      let dir = move > 0 ? (isX ? "left" : "down") : isX ? "right" : "up";
      if (
        (typeof this.disabledScrollDir == "string" &&
          this.disabledScrollDir == dir) ||
        (this.disabledScrollDir &&
          Array.isArray(this.disabledScrollDir) &&
          this.disabledScrollDir.includes(dir))
      ) {
        return true;
      }
    },
    touchStart(event, isChangeMode) {
      if (!isChangeMode && this.changeModeTimer)
        clearInterval(this.changeModeTimer);

      !isChangeMode && this.$emit("touchStart", event);

      this.touchStartPositionX = event.touches[0].clientX;
      this.touchStartPositionY = event.touches[0].clientY;
      this.touch = {
        x: 0,
        y: 0,
      };
      this.isTouching = true;
      this.disAnimation = false;
      this.scrollingDir = "";
    },
    touchMove(event, isChangeMode) {
      !isChangeMode && this.$emit("touchMove", event);

      let moveX = event.touches[0].clientX - this.touchStartPositionX;
      let moveY = event.touches[0].clientY - this.touchStartPositionY;

      if (
        (this.scrollingDir == "" && Math.abs(moveX) > Math.abs(moveY) + 10) ||
        this.scrollingDir == "X"
      ) {
        if (this.disableScroll(true, moveX)) return;

        this.touch = {
          x: moveX / this.$refs.calendar.offsetWidth,
          y: 0,
        };
        this.scrollingDir = "X";
        this.slideStatus = "def";
      } else {
        if (this.disableScroll(false, moveY)) return;

        this.touch = {
          x: 0,
          y: moveY,
        };

        if (Math.abs(moveY) > 5) {
          this.scrollingDir = "Y";

          //如果当前是周模式，设置isShowWeek来切换到月模式，isWeekMode不变，以完成切换动画展示
          if (this.isWeekMode) this.isShowWeek = false;
        }

        !this.isShowWeek && this.calTransCalendarY();
      }
    },
    touchEnd(event, isChangeMode) {
      !isChangeMode && this.$emit("touchEnd", event);

      this.isTouching = false;

      if (
        Math.abs(this.touch.x) > Math.abs(this.touch.y) &&
        Math.abs(this.touch.x) > 0.1
      ) {
        this.toMonth(this.touch.x < 0 ? "next" : "pre");
      }
      if (Math.abs(this.touch.y) > Math.abs(this.touch.x)) {
        this.setCalendarY(isChangeMode);
      } else {
        this.touch = {
          x: 0,
          y: 0,
        };
      }
    },
    calTransCalendarY() {
      let calendarcon = this.$refs.calendarcon;
      let rowHeight = this.height / 6;
      let offsetY = this.getCurDatesOffsetY();

      if (this.isWeekMode && this.touch.y > 0) {
        calendarcon.style.height =
          Math.min(rowHeight + this.touch.y, this.height) + "px";
        this.rowTranslateY = Math.min(this.touch.y - offsetY, 0) + "px";
      } else if (!this.isWeekMode && this.touch.y < 0) {
        calendarcon.style.height =
          Math.max(this.height + this.touch.y, rowHeight) + "px";
        this.rowTranslateY = Math.max(this.touch.y, -offsetY) + "px";
      }
    },
    setCalendarY(isChangeMode) {
      let calendarcon = this.$refs.calendarcon;
      let rowHeight = this.height / 6;
      let offsetY = this.getCurDatesOffsetY();
      let tempHeight = calendarcon.style.height;

      if (Math.abs(this.touch.y) > 50) {
        if (!this.isWeekMode && this.touch.y < 0) {
          calendarcon.style.height = rowHeight + "px";
          this.isWeekMode = !this.isWeekMode;
          this.rowTranslateY = -offsetY + "px";
        } else if (this.isWeekMode && this.touch.y > 0) {
          calendarcon.style.height = this.height + "px";
          this.isWeekMode = !this.isWeekMode;
          this.rowTranslateY = 0;
        }
      } else {
        if (this.isWeekMode) {
          calendarcon.style.height = rowHeight + "px";
          this.rowTranslateY = -offsetY + "px";
        } else {
          calendarcon.style.height = this.height + "px";
          this.rowTranslateY = 0;
        }
      }

      this.isChangeMode = true;
      setTimeout(
        () => {
          !isChangeMode &&
            Math.abs(this.touch.y) > 50 &&
            this.$emit("slideEnd", this.isWeekMode ? "up" : "down");

          if (this.isWeekMode) {
            this.isShowWeek = true;
            this.rowTranslateY = 0;

            //从月模式切换到周模式因为rowTranslateY修改需要设置disAnimation来禁止动画，防止切换完成后又出现
            //rowTranslateY的修改导致的偏移动画
            this.disAnimation = true;
          }

          this.touch = {
            x: 0,
            y: 0,
          };
          this.isChangeMode = false;
        },
        tempHeight != calendarcon.style.height
          ? this.transitionDuration * 1000
          : 0
      );
    },

    getCurDateByWeek(type) {
      let year = this.currentDate.getFullYear();
      let month = this.currentDate.getMonth();
      let day = this.currentDate.getDate();

      let days = this.getMonthDays(year, month);
      let week = new Date(year, month, 1).getDay();
      let week_last = new Date(year, month, days).getDay();

      let last = this.getLastYearMon(year, month);
      let last_days = this.getMonthDays(last.year, last.month);

      let weekYear = year,
        weekMonth = month,
        weekDay = day + (type == "pre" ? -7 : 7);

      if (weekDay <= 0) {
        if (day <= 7 - week) {
          weekDay = last_days;
          weekMonth = month - 1;
        } else weekDay = 1;
      } else if (weekDay > days) {
        if (day >= days - week_last) {
          weekDay = 1;
          weekMonth = month + 1;
        } else weekDay = days;
      }

      if (weekMonth < 0) {
        weekMonth = 11;
        weekYear = year - 1;
      } else if (weekMonth > 11) {
        weekMonth = 0;
        weekYear = year + 1;
      }

      let date = new Date();

      date.setFullYear(weekYear, weekMonth, weekDay);
      this.validateDate(date, type);
    },
    getCurDateByMonth(type) {
      let year = this.currentDate.getFullYear();
      let month = this.currentDate.getMonth();
      let day = this.currentDate.getDate();

      let last = this.getLastYearMon(year, month);
      let last_days = this.getMonthDays(last.year, last.month);

      let next = this.getNextYearMon(year, month);
      let next_days = this.getMonthDays(next.year, next.month);

      let date = new Date();
      if (type == "pre")
        date.setFullYear(last.year, last.month, Math.min(day, last_days));
      else date.setFullYear(next.year, next.month, Math.min(day, next_days));

      this.validateDate(date, type);
    },
    toMonth(type) {
      if (this.disableScroll(true, type == "pre" ? 50 : -50)) return;

      this.isWeekMode
        ? this.getCurDateByWeek(type)
        : this.getCurDateByMonth(type);
      this.translateIndex += type == "pre" ? 1 : -1;

      this.$emit("input", this.currentDate);
      this.$emit("change", this.currentDate);
      setTimeout(() => {
        this.$emit("slideEnd", type == "pre" ? "left" : "right");
      }, this.transitionDuration * 1000);
    },

    selDate(item) {
      if (this.isHideDate(item) || this.isDisableDate(item.date)) return;

      this.$emit("input", item.date);
      this.$emit("change", item.date);
      this.$emit("click", item.date);

      this.currentDate = item.date;
      if (item.dateType == "pre") this.translateIndex += 1;
      else if (item.dateType == "next") this.translateIndex -= 1;
    },

    toPre() {
      this.disAnimation = false;

      this.toMonth("pre");
    },
    toNext() {
      this.disAnimation = false;

      this.toMonth("next");
    },
    toToday() {
      this.disAnimation = false;

      this.currentDate = this.today;
      this.$emit("input", this.currentDate);
      this.$emit("change", this.currentDate);
    },
    changeMode() {
      if (this.isChangeMode) return;
      if (this.disableScroll(false, this.isWeekMode ? 50 : -50)) return;

      let event = {
        touches: [
          {
            clientX: 0,
            clientY: 0,
          },
        ],
      };

      this.isChangeMode = true;
      this.touchStart(event, true);

      let step = this.height / (this.transitionDuration * 200);
      if (this.changeModeTimer) clearInterval(this.changeModeTimer);
      this.changeModeTimer = setInterval(() => {
        event.touches[0].clientY += step * (this.isWeekMode ? 1 : -1);

        if (
          Math.abs(event.touches[0].clientY) > this.height &&
          this.changeModeTimer
        ) {
          clearInterval(this.changeModeTimer);

          this.touchEnd(event, true);
          this.isChangeMode = false;
        }

        this.touchMove(event, true);
      }, 5);
    },
  },
};
</script>
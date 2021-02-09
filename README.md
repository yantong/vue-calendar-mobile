# vue-calender-mobile
* 基于vue的移动端日历组件
* 支持月和周模式的切换
* 滑动和切换模式都有较好的动画效果
* 提供自定义类、slot和方法，满足自定义的需求

    > 参考了[vue-hash-calendar](https://github.com/TangSY/vue-hash-calendar),这里表示感谢
    
# 图例和demo
基础功能：

![基础功能](./package/img/example.gif)

其他功能可查看demo：复制此仓库代码，安装依赖然后运动即可查看其他功能的示例,也可直接访问[demo](https://yantong.github.io/leaf/#/Calendar)

  > github: [vue-calender-mobile](https://github.com/yantong/vue-calendar-mobile)

# 安装

```
npm i vue-calender-mobile -S
```

# 引入

在 main.js 中写入以下内容：

```javascript
import Vue from 'vue'
import App from './App.vue'
import Calendar from 'vue-calender-mobile'
import  'vue-calender-mobile/lib/vue-calender-mobile.css'

Vue.use(Calendar)

new Vue({
  render: h => h(App),
}).$mount('#app')
```

# 使用

```
// 在VUE文件中引入组件
<vue-calender-mobile></vue-calender-mobile>
 ```

# Attributes
| 参数      | 说明    | 类型      | 可选值       | 默认值   |
|---------- |-------- |---------- |-------------  |-------- |
| value / v-model | 输入绑定值 | Date | - | 当天时间 |
| hideNotCurMonDay | 是否隐藏不是当月的的日期| Boolean | - | false |
| defaultWeekMode | 默认是否是周模式 | Boolean | - | false |
| transitionDuration | 动画时间（单位秒） | Number | - | 0.3 |
| firstDayOfWeek | 以星期几为一周开始 | Number | 1 到 7 | 7 |
| maxDate | 最大日期 | Date | - | - |
| minDate | 最小日期 | Date | - | - |
| disabledDates | 禁用日期（当类型为Function时，返回true则是表示禁用） | Array, Function(Date) | - | - |
| disDateStrategy | 当滑动时选中的日期为禁用日期时提供的策略，此方法需要返回一个Date，如果滑动后选择的日期是禁用日期，此方法返回的Date将成为滑动后新的选中日期而不是默认的滑动后的日期，参数时默认选中的禁用日期 | Function(Date) | - | - |
| disabledScrollDir | 禁用的滑动方向（当类型为Array时，值可以为可选值的任意搭配） | String，Array | 'left','right','up','down' | - |
| dateClassName | 日期的自定义类 | String | - | - |
| disDateClassName | 禁用日期的自定义类 | String | - | - |
| todayClassName | 日期为当天的自定义类 | String | - | - |
| curDateClassName | 当前选中日期的自定义类 | String | - | - |
| notCurMonDateClassName | 不是本月日期的自定义类 | String | - | - |


# Slots

| name | 说明 |
|------|--------|
| header | 头部内容 |
| weekday | 周所在行的显示内容 |
| day | 日期显示内容（参数为：{ dateType, date }， dateType值为‘cur’、‘pre’、’next‘其中之一，date为显示的日期） |
| footer | 底部内容 |


# Events
| 事件名称 | 说明 | 回调参数 |
|---------|--------|---------|
| change | 选中日期修改事件 | 选中日期 |
| click | 日期点击事件 | 点击日期 |
| touchStart | 触摸开始事件 | touch事件的event对象 |
| touchMove | 触摸移动事件 | touch事件的event对象 |
| touchEnd | 触摸结束事件 | touch事件的event对象 |
| slideEnd | 滑动结束事件 | 'left','right','up','down' |

# Methods

| 方法名 | 说明 | 参数 |
| ---- | ---- | ---- |
| toPre | 滑动到上一页 | - |
| toNext | 滑动到下一页 | - |
| toToday | 返回到今天 | - |
| changeMode | 切换月和周的模式 | - |
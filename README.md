# Vue.js 实现日历效果

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gi7s4psn3vj30ze0igq3d.jpg)

[Demo](https://liuzhenghe30265.github.io/vue-calendar/)

[Github](https://github.com/liuzhenghe30265/vue-calendar)

# 功能需求

使用 Vue.js 实现指定年月的日历表，配合后台接口数据，添加对应日期的空气质量描述等信息。后台传递当月空气质量指数的数据，前端生成日历表后将空气质量指数添加到对应日期上。

空气质量数据示例：

data.json

```

{
  "code": 200,
  "msg": "",
  "data": [{
      "time": "2020-08-01",
      "kqzl": "优"
    },
    {
      "time": "2020-08-02",
      "kqzl": "良"
    },
    {
      "time": "2020-08-03",
      "kqzl": "良"
    }
  ]
}
```

# 实现方法

```vue
<template>
  <div
    id="app">
    <h1
      style="text-align:center;">
      2020-08
    </h1>
    <div
      class="calendar-container">
      <div
        class="calendar-week">
        <div
          class="cw-inner">
          <div
            class="cw-item"
            :style="{width: setItemWidth}"
            v-for="(item, index) of week"
            :key="index">
            {{item}}
          </div>
        </div>
      </div>
      <div
        class="calendar-day">
        <div
          class="cd-list"
          v-for="(item, index) of day"
          :key="index">
          <div
            class="cl-item"
            :style="{width: setItemWidth}"
            v-for="(child,index) of item"
            :key="index"
            :class="[{has: child}]">
            <div
              class="ci-inner"
              v-if="child">
              <span>{{child.date}}</span>
              <span
                v-if="child.text"
                class="aqi"
                :class="child.text.kqzl">
                {{child.text.kqzl}}
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import kqzlData from './assets/data.json' // 空气质量数据
export default {
  name: 'app',
  data () {
    return {
      week: [
        '星期日',
        '星期一',
        '星期二',
        '星期三',
        '星期四',
        '星期五',
        '星期六',
      ],
      day: [],
    }
  },
  computed: {
    setItemWidth () {
      return 100 / 7 + '%'
    },
  },
  mounted () {
    this.createCalendar(2020, 8)
  },
  methods: {
    /**
     * @name: 格式化日期
     * @param {date}
     */
    dateFormat (date) {
      let dateArr = date.split('-')
      let month = dateArr[1] >= 10 ? dateArr[1] : '0' + dateArr[1]
      let day = dateArr[2] >= 10 ? dateArr[2] : '0' + dateArr[2]
      return dateArr[0] + '-' + month + '-' + day
    },

    /**
     * @name: 日期信息
     * @param {date}
     */
    getDayInfo (date) {
      let kqzl = kqzlData.data
      let formatDate = this.dateFormat(date)
      let txt = kqzl[kqzl.findIndex(item => item.time === formatDate)]
      return txt
    },

    /**
     * @name: 生成日历表
     * @param {year}
     * @param {month}
     */
    createCalendar (year, month) {
      // 某个月一共有多少天
      let allDay = new Date(year, month, 0).getDate()
      // 某个月1号是星期几
      let firstDay = this.judjeFirstDay(year, month)
      // 需要多少行来展示
      let row = Math.ceil((allDay + firstDay) / 7)
      let k = firstDay
      let result = []
      let count = 1
      // 生成日历二维数组
      for (let i = 0; i < row; i++) {
        result[i] = new Array(7)
        do {
          if (count <= allDay) {
            result[i][k] = {
              date: count,
              // 根据接口匹配日期对应的信息
              text: this.getDayInfo(year + '-' + month + '-' + count),
            }
            k++
            count++
          } else {
            break
          }
        } while (k < 7)
        k = 0
      }
      this.day = result
    },

    /**
     * @name: 判断某年某月1号是星期几
     * @param {year}
     * @param {month}
     */
    judjeFirstDay (year, month) {
      const date = new Date(year, month - 1, 1)
      const week = date.getDay()
      return week
    },
  },
}
</script>

<style lang="scss">
.calendar-container {
  text-align: center;
  .calendar-week {
    margin-bottom: 4px;
    padding: 0 4px;
    color: #fff;
    .cw-inner {
      overflow: hidden;
      background: #a0a0a0;
      .cw-item {
        float: left;
        padding: 8px 0;
      }
    }
  }
  .calendar-day {
    .cd-list {
      overflow: hidden;
      .cl-item {
        float: left;
        min-height: 30px;
        box-sizing: border-box;
        padding: 4px;
        .ci-inner {
          background: #f5f5f5;
          padding: 8px 0;
          span {
            display: inline-block;
            &.aqi {
              color: #fff;
              background: #a7cf8c;
              padding: 0 4px;
              border-radius: 4px;
            }
            &.优 {
              background: #a7cf8c;
            }
            &.良 {
              background: #f7da64;
            }
            &.轻度 {
              background: #f29e39;
            }
            &.中度 {
              background: #da555d;
            }
            &.重度 {
              background: #b9377a;
            }
            &.严重 {
              background: #881326;
            }
          }
        }
      }
    }
  }
}
</style>
```

## Build Setup

```bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).

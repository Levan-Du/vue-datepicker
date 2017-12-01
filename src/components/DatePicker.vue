<template>
    <div class="datepicker">
        <input type="text" :name="name" :value="selectedDate">
        <div class="panel">
            <header>
                <button>|&lt;&lt;</button>
                <button>&lt;</button>
                <input class="text-month" type="text" name="month" :value="month+1">
                <button>&gt;</button>
                <button>&gt;&gt;|</button>
                <input class="text-year" type="text" name="year" :value="year">
            </header>
            <div class="main">
                <div class="year"></div>
                <div class="month"></div>
                <div class="date">
                    <table>
                        <thead>
                            <tr class="row-header">
                                <th class="des">周</th>
                                <th>一</th>
                                <th>二</th>
                                <th>三</th>
                                <th>四</th>
                                <th>五</th>
                                <th>六</th>
                                <th>日</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr class="row" v-for="days in dates">
                                <td></td>
                                <td v-for="d in days" :class="{gray:isSameMonth(d),selected:isSelectedDate(d)}" @click="onSelectDate(d)">{{d.date}}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
            <footer>
                <button>今天</button>
                <button>清空</button>
                <button>确定</button>
            </footer>
        </div>
    </div>
</template>
<script>
export default {
    props: ['name'],
    data() {
        return {
            year: '',
            month: '',
            day: '',
            date: '',
            dates: []
        }
    },
    methods: {
        keyup() {

        },
        newDate(year, month, date) {
            return new Date(year, month, date)
        },
        getDatesCount(year, month) {
            var curDate = this.newDate(year, month, 1)

            /*  生成实际的月份: 由于curMonth会比实际月份小1, 故需加1 */
            curDate.setMonth(month + 1)

            /* 将日期设置为0, 这里为什么要这样设置, 我不知道原因, 这是从网上学来的 */
            curDate.setDate(0)
            /* 返回当月的天数 */
            return curDate.getDate()
        },
        formatDay(day) {
            switch (day) {
                case 0:
                    return '日'
                case 1:
                    return '一'
                case 2:
                    return '二'
                case 3:
                    return '三'
                case 4:
                    return '四'
                case 5:
                    return '五'
                case 6:
                    return '六'
            }
        },
        getCellDate(year, month, day, date) {
            return {
                year: year,
                month: month,
                day: day,
                dayDes: this.formatDay(day),
                date: date,
            }
        },
        getDates({ year, month, day, date, dateCount }, maxDate = 49) {
            var curDate = this.newDate(year, month, date)
            var dates = [];
            for (var i = 1; i <= dateCount; i++) {
                curDate.setDate(i)
                var oDate = this.getCellDate(year, month, curDate.getDay(), curDate.getDate())

                dates.push(oDate)
            }

            var preDate = this.newDate(year, month - 1, 1)
            var preYear = preDate.getFullYear()
            var preMonth = preDate.getMonth()
            var preMonthDateCount = this.getDatesCount(preYear, preMonth)
            var firstDay = dates[0].day
            var l = 7 + firstDay
            for (; --l;) {
                var oDate = this.getCellDate(year, preMonth, l % 7, preMonthDateCount--)
                dates.unshift(oDate)
            }

            var nextMonthStart = 1
            var nextDate = this.newDate(year, month + 1, 1)
            var nextMonth = nextDate.getMonth()
            var nextYear = nextDate.getFullYear()
            var endDay = dates[dates.length - 1].day
            var nextLen = maxDate - dates.length;
            for (; nextLen--;) {
                var oDate = this.getCellDate(nextYear, nextMonth, endDay % 7, nextMonthStart++)
                dates.push(oDate)
            }

            return dates
        },
        isSameMonth({ year, month }) {
            return !(year == this.year && month == this.month)
        },
        isSelectedDate({ year, month, date }) {
            return (year == this.year && month == this.month && date == this.date)
        },
        onSelectDate(d) {
            this.fillDates({
                year: d.year,
                month: d.month,
                day: d.day,
                date: d.date
            })
        },
        fillDates({ year, month, day, date }) {
            var curDate = this.newDate(year, month, date)
            var dateCount = this.getDatesCount(year, month)
            var dates = this.getDates({ year, month, day, date, dateCount })

            this.year = curDate.getFullYear()
            this.month = curDate.getMonth()
            this.day = curDate.getDay()
            this.date = curDate.getDate()

            var t = []
            this.dates = []
            dates.forEach((el, i) => {
                t.push(el)
                if (i !== 0 && (i + 1) % 7 === 0) {
                    this.dates.push(t)
                    t = []
                }
            })
        }
    },
    computed: {
        selectedDate() {
            return (this.year) + '-' + (this.month + 1) + '-' + this.date
        }
    },
    mounted() {
        var date = new Date()
        this.fillDates({
            year: date.getFullYear(),
            month: date.getMonth(),
            day: date.getDay(),
            date: date.getDate()
        })
    }
}
</script>
<style>
.datepicker {
    display: inline-block;
}

.panel {
    border: solid 1px #ccc;
}

.panel header {
    background: #3ee;
}

.panel header>* {
    background: transparent;
    border: solid 1px #ccc;
    font-weight: bold;
}

.text-year {
    width: 100px;
}

.text-month {
    width: 100px;
}

.date table {
    width: 100%;
    border-collapse: collapse;
}

.date table thead {
    background: #8ff;
}

.date table tbody {
    background: #cff;
}

.panel footer {
    background: #3ff;
    text-align: right;
}

.des {
    width: 30px;
}

.gray {
    color: #999;
}

td {
    padding-left: 10px;
}

td:hover {
    cursor: pointer;
    background: #3ee;
}

.selected {
    background: #3ee;
}
</style>
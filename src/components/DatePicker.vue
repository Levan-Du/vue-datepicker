<template>
    <div class="datepicker">
        <input type="text" :name="name" :value="selectedDate">
        <div class="panel">
            <header>
                <button>|&lt;&lt;</button>
                <button>&lt;</button>
                <input class="text-month" type="text" name="month" :value="month">
                <input class="text-year" type="text" name="year" :value="year">
                <button>&gt;</button>
                <button>&gt;&gt;|</button>
            </header>
            <div class="main">
                <div class="year"></div>
                <div class="month"></div>
                <div class="date">
                    <table>
                        <thead>
                            <tr class="row-header">
                                <th class="des">星期</th>
                                <th>日</th>
                                <th>一</th>
                                <th>二</th>
                                <th>三</th>
                                <th>四</th>
                                <th>五</th>
                                <th>六</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr class="row" v-for="el in dates">
                                <td></td>
                                <td v-for="d in el" :class="{gray:isSameMonh(d.month),selected:isSelectedDate(d.date)}" @click="onSelectDate(d)">{{d.date}}</td>
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
        getDate(year, month, date) {
            var curDate = new Date
            curDate.setFullYear(year)
            curDate.setMonth(month)
            curDate.setDate(date)
            return curDate
        },
        getDatesCount(year, month, date = 1) {
            var curDate = this.getDate(year, month, date)

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
        getDates(year, month, day, date) {
            var curDate = this.getDate(year, month, date)
            var dateCount = this.getDatesCount(year, month, date)

            var dates = [];
            for (var i = 1; i <= dateCount; i++) {
                curDate.setDate(i)
                var oDate = this.getCellDate(year, month + 1, curDate.getDay(), curDate.getDate())

                dates.push(oDate)
            }

            var preMonthDateCount = this.getDatesCount(year, month - 1)
            var firstDay = dates[0].day
            var l = 7 + firstDay
            for (; --l;) {
                var oDate = this.getCellDate(year, month, l % 7, preMonthDateCount--)
                dates.unshift(oDate)
            }

            var nextMonthStart = 1
            var endDay = dates[dates.length - 1].day
            var r = 49 - dates.length;
            for (; r--;) {
                var oDate = this.getCellDate(year, month + 2, endDay % 7, nextMonthStart++)
                dates.push(oDate)
            }

            return dates
        },
        isSameMonh(month) {
            return month !== this.month + 1
        },
        isSelectedDate(date) {
            return date === this.date
        },
        onSelectDate(d) {
            this.year = d.year
            this.month = d.month
            this.date = d.date
        }
    },
    computed: {
        selectedDate() {
            return this.year + '-' + this.month + '-' + this.date
        }
    },
    mounted() {
        var date = new Date()
        this.year = 1900 + date.getYear()
        this.month = date.getMonth()
        this.day = date.getDay()
        this.date = date.getDate()

        var dates = this.getDates(this.year, this.month, this.day, this.date)
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

.text-year {}

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
    width: 50px;
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
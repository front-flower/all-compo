<template>
<div>
    <h1>hello</h1>
    <h4>{{ year }} {{ month + 1 }}</h4>
    <div class="date-box">
        <ul class="text-day">
            <li>일</li>
            <li>월</li>
            <li>화</li>
            <li>수</li>
            <li>목</li>
            <li>금</li>
            <li>토</li>
        </ul>
        <ul class="day">
            <li v-for="(item, idx) in dateList" :key="idx">{{item}}</li>
        </ul>
    </div>
    <button @click="prev">prev</button>
    <button @click="next">next</button>
</div>
</template>
<script>
import { defineComponent } from 'vue'

export default defineComponent({
    name: 'full-calendar',
    data () {
        return {
            year: '',
            month: '',
            dateList: []
        }
    },
    created () {
        this.setCalendar()
    },
    methods: {
        prev () {
            this.setCalendar()
        },
        next () {
            this.setCalendar()
        },
        setCalendar () {
            let date = new Date()
            this.year = date.getFullYear()
            this.month = date.getMonth()
            
            let start = new Date(this.year, this.month, 1)
            let last = new Date(this.year, this.month, 0).getDate()
            let arr = []

            let day = start.getDay()
            console.log(day)
            for (let i = 0; i < day; i++) {
                arr.push(' ')
            }

            for (let i = 1; i <= last; i++) {
                arr.push(i)
            }
            this.dateList = arr
        }
    }
})
</script>
<style lang="scss" scoped>
ul {
    padding: 0;
    margin: 0;
    list-style-type: none;
    li {
        width: 25px;
    }
}
.date-box {
    width: 175px;
    margin: 0 auto;
}
.text-day {
    display: flex;
    & li:nth-child(1) {
        color: red;
    }
    & li:nth-child(7) {
        color: blue;
    }
}
.day {
    width: 175px;
    flex-wrap: wrap;
    display: flex;
}
</style>
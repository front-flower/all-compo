<template>
<div>
    <h4>{{ year }}년 {{ month + 1 }}월</h4>
    <button class="prev" @click="prev">prev</button>
    <button class="next" @click="next">next</button>
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
            <li v-for="(item, idx) in dateList" :key="idx">
                {{ item.date }}
                <template v-for="(plan, idx) in item.plans" :key="idx">
                    <div 
                        class="long-term"
                        :style="{ backgroundColor: plan.color }" 
                        v-if="plan.range && plan.range[0] <= item.date && plan.range[1] >= item.date">
                        {{ plan.title }}
                    </div>
                    <p class="booking" v-if="!plan.range">
                        <span :style="{ backgroundColor: plan.color }"></span>{{ plan.title }}
                    </p>
                </template>
            </li>
        </ul>
    </div>
</div>
</template>
<script>
import { defineComponent } from 'vue'

export default defineComponent({
    name: 'full-calendar',
    props: ['data'],
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
            this.setCalendar('prev')
        },
        next () {
            this.setCalendar('next')
        },
        setData () {
            let arr = JSON.parse(JSON.stringify(this.data))

            for (let i = 0; i < this.dateList.length; i++) {
                let ddd = this.dateList[i]

                for (let item of arr) {
                    
                    if (item.date.includes('~')) {
                        let split = item.date.split('~')
                        let year = Number(split[0].slice(0, 4))
                        let month = Number(split[0].slice(5, 7))
                        
                        let startDay = Number(split[0].slice(-2))
                        let endDay = Number(split[1].slice(-2))

                        if (this.year === year && this.month === month - 1 && Number(ddd.date) >= startDay && Number(ddd.date) <= endDay) {
                            item.range = [Number(startDay), Number(endDay)]
                            console.log(item.range)
                        }
                        ddd.plans.push(item)
                    } else {
                        let year = Number(item.date.slice(0, 4))
                        let month = Number(item.date.slice(5, 7))
                        let day = Number(item.date.slice(-2))

                        if (this.year === year && this.month === month - 1 && Number(ddd.date) === day) {
                            ddd.plans.push(item)
                        }
                    }
    
                }
            }
        },
        setCalendar (keyword) {
            let date
            if (keyword === 'prev') {
                date = new Date()
                if (!this.month) {
                    this.year -= 1
                    this.month = 11
                } else {
                    this.month -= 1
                }
            } else if (keyword === 'next') {
                date = new Date()
                if (this.month === 11) {
                    this.month = 0
                    this.year += 1
                } else {
                    this.month += 1
                }
            } else {
                date = new Date()
                this.year = date.getFullYear()
                this.month = date.getMonth()
            }
            
            let start = new Date(this.year, this.month, 1)
            let last = new Date(this.year, this.month + 1, 0).getDate()
            let arr = []

            let day = start.getDay()

            for (let i = 0; i < day; i++) {
                arr.push({ date: ' ', plans: [] })
            }

            for (let i = 1; i <= last; i++) {
                arr.push({ date: i, plans: [] })
            }
            this.dateList = arr
            this.setData()
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
    width: 700px;
    margin: 0 auto;
}
.text-day {
    display: flex;
    font-weight: 500;
    li {
        width: 100px;
        padding-bottom: 10px;
    }
    & li:nth-child(1) {
        color: red;
    }
    & li:nth-child(7) {
        color: blue;
    }
}
.booking {
    font-size: 12.5px;
    span {
        width: 7.5px;
        height: 7.5px;
        background-color: palevioletred;
        border-radius: 50%;
        display: inline-block;
        margin-right: 5px;
    }
}
.day {
    width: 700px;
    flex-wrap: wrap;
    display: flex;
    li {
        text-align: left;
        width: 100px;
        height: 100px;
        padding: 10px;
    }
}
* {
    box-sizing: border-box;
}
p {
    margin: 0;
}
button {
    border: none;
    color: white;
    padding: 5px;
}
.prev {
    background-color: skyblue;
    margin-right: 5px;
}
.next {
    background-color: green;
}
.long-term {
    font-size: 12.5px;
    position: absolute;
    width: 100px;
}
</style>
<script setup>

import { inject, ref, onMounted } from 'vue';

const view_date = inject('view_date')
const view_length = inject('view_length')
const get_display_width = inject('get_display_width')
const get_length = inject('get_length')
const num2hanzi = inject('num2hanzi')

const get_column_type = () => {
    let opt = view_length.value.options[view_length.value.position]
    switch(opt){
        case 'full_year':
            return 'month'
        case 'half_year':
            return 'month'
        case 'three_months':
            return 'month'
        case 'two_months':
            return 'week'
        case 'month':
            return 'week'
        case 'three_weeks':
            return 'day'
        case 'two_weeks':
            return 'day'
        case 'week':
            return 'day'
        case 'five_days':
            return 'half_day'
        case 'three_days':
            return 'half_day'
        case 'day':
            return 'hour'
    // return in [ month, week, day, half_day, hour ]
    }
}

const get_column_edges = () => {
    let column_type = get_column_type()
    let length = get_length()
    let left_date = view_date.value
    let temp_date = left_date

    let res = []

    // type in [ month, week, day, half_day, hour ]
    if (column_type === 'month') {
        while(1){
            temp_date = new Date(temp_date.getFullYear(), temp_date.getMonth()+1, 1)
            if (temp_date-left_date>=length) break;
            res.push(temp_date)
        }
    } else if (column_type === 'week'){ // get every Monday
        temp_date = new Date(
            temp_date.getFullYear(),
            temp_date.getMonth(),
            temp_date.getDate()-( temp_date.getDay()===0 ? 7 : temp_date.getDay() )+8)
        res.push(temp_date)
        while(1){
            temp_date = new Date(temp_date.getFullYear(), temp_date.getMonth(), temp_date.getDate()+7)
            if (temp_date-left_date>=length) break;
            res.push(temp_date)
        }
        
    } else if (column_type === 'day'){
        while(1){
            temp_date = new Date(temp_date.getFullYear(), temp_date.getMonth(), temp_date.getDate()+1)
            if (temp_date-left_date>=length) break;
            res.push(temp_date)
        }
    } else if (column_type === 'half_day'){ // 6:00 - 18:00 for day, and other for night
        if (temp_date.getHours()<6) {
            temp_date = new Date(temp_date.getFullYear(), temp_date.getMonth(), temp_date.getDate(), 6, 0, 0)
        } else if (temp_date.getHours()<18){
            temp_date = new Date(temp_date.getFullYear(), temp_date.getMonth(), temp_date.getDate(), 18, 0, 0)
        } else {
            temp_date = new Date(temp_date.getFullYear(), temp_date.getMonth(), temp_date.getDate(), 18+12, 0, 0)
        }
        res.push(temp_date)
        while(1){
            temp_date = new Date(temp_date.getFullYear(), temp_date.getMonth(), temp_date.getDate(), temp_date.getHours()+12, 0, 0)
            if (temp_date-left_date>=length) break;
            res.push(temp_date)
        }
    } else if (column_type === 'hour'){
        while(1){
            temp_date = new Date(temp_date.getFullYear(), temp_date.getMonth(), temp_date.getDate(), temp_date.getHours()+1, 0, 0)
            if (temp_date-left_date>=length) break;
            res.push(temp_date)
        }
    }
    res.push(new Date(Number(left_date)+length))
    return res
}

const get_column_settings = () => {
    let column_list = get_column_edges()
    let column_type = get_column_type()
    let screen_width = get_display_width()
    let screen_length = get_length()
    let left_date = view_date.value
    let res = []

    let i = 1
    if (column_type === 'month') {
        res.push({
            name: num2hanzi(left_date.getMonth()+1)+'月',
            width: (column_list[0]-left_date)/screen_length*screen_width,
            class: left_date.getMonth()%2 == 0 ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
        })
        for (i=1; i<column_list.length; i++) {
            res.push({
                name: num2hanzi(column_list[i-1].getMonth()+1)+'月',
                width: (column_list[i]-column_list[i-1])/screen_length*screen_width,
                class: column_list[i-1].getMonth()%2 == 0 ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
            })
        }
    } else if (column_type === 'week') {
        res.push({
            name: (left_date.getMonth()+1)+'.'+left_date.getDate()+'起',
            width: (column_list[0]-left_date)/screen_length*screen_width,
            class: column_list[0].getDate()%2 == 0 ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
        })
        for (i=1; i<column_list.length; i++) {
            res.push({
                name: (column_list[i-1].getMonth()+1)+'.'+column_list[i-1].getDate()+'起',
                width: (column_list[i]-column_list[i-1])/screen_length*screen_width,
                class: res[res.length-1].class == 'TableFrame_TimeTable_Columns_Light' ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
            })
        }
    } else if (column_type === 'day') {
        res.push({
            name: (left_date.getMonth()+1)+'.'+left_date.getDate(),
            width: (column_list[0]-left_date)/screen_length*screen_width,
            class: left_date.getDay()%6 == 0 ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
        })
        for (i=1; i<column_list.length; i++) {
            res.push({
                name: (column_list[i-1].getMonth()+1)+'.'+column_list[i-1].getDate(),
                width: (column_list[i]-column_list[i-1])/screen_length*screen_width,
                class: column_list[i-1].getDay()%6 == 0 ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
            })
        }
    } else if (column_type === 'half_day') {
        res.push({
            name: (left_date.getMonth()+1)+'.'+left_date.getDate()+(left_date.getHours()<6 || left_date.getHours()>=18 ?'夜':'昼'),
            width: (column_list[0]-left_date)/screen_length*screen_width,
            class: (left_date.getHours()<6 || left_date.getHours()>=18) ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
        })
        for (i=1; i<column_list.length; i++) {
            res.push({
                name: (column_list[i-1].getMonth()+1)+'.'+column_list[i-1].getDate()+(column_list[i-1].getHours()<6 || column_list[i-1].getHours()>=18 ?'夜':'昼'),
                width: (column_list[i]-column_list[i-1])/screen_length*screen_width,
                class: (column_list[i-1].getHours()<6 || column_list[i-1].getHours()>=18) ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
            })
        }
    } else if (column_type === 'hour') {
        res.push({
            name: left_date.getHours(),
            width: (column_list[0]-left_date)/screen_length*screen_width,
            class: left_date.getHours()%2==0 ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
        })
        for (i=1; i<column_list.length; i++) {
            res.push({
                name: column_list[i-1].getHours(),
                width: (column_list[i]-column_list[i-1])/screen_length*screen_width,
                class: column_list[i-1].getHours()%2==0 ? 'TableFrame_TimeTable_Columns_Dark' : 'TableFrame_TimeTable_Columns_Light'
            })
        }
    }
    return res
}


// force refresh when loading or resizing
const refresh_in_tableframe = ref('refresh_the_div')
const refrashfunc_in_tableframe = () => {refresh_in_tableframe.value=''+Number(new Date())}
onMounted( () => { refrashfunc_in_tableframe() } )
window.addEventListener("resize", refrashfunc_in_tableframe, false)


// const f = () => { console.log(get_column_settings()) }

</script>

<template>
    <div class="TableFrame">
        <div class="TableFrame_Tasks"></div>
        <!-- <div class="TableFrame_Tasks" @click="f"></div> -->
        <div class="TableFrame_TimeTable">
            <div class="TableFrame_TimeTable_Columns"
                 v-for="item of get_column_settings()"
                 :style="{width:item.width+'px'}"
                 :class="item.class"
                 :key="refresh_in_tableframe"
            >{{ item.name }}</div>
            <!-- <div class="TableFrame_TimeTable_Columns">123</div>
            <div class="TableFrame_TimeTable_Columns TableFrame_TimeTable_Columns_Light">123</div>
            <div class="TableFrame_TimeTable_Columns TableFrame_TimeTable_Columns_Dark">123</div>
            <div class="TableFrame_TimeTable_Columns">xxxx</div> -->
        </div>
    </div>
</template>

<style scoped>
    .TableFrame {
        position: fixed;
        width: 100%;
        height: 110%;
        background-color: rgba(33, 0, 167, 0.657);
        font-size: 20px;
        -webkit-user-select: none;
        user-select: none;
        white-space: nowrap;
    }

    .TableFrame_Tasks {
        display: inline-block;
        vertical-align: top;
        width: 300px;
        height: 100%;
        background-color: #EEEEEE;
        /* box-shadow: 5px 0px 5px #DDDDDD; */
    }

    .TableFrame_TimeTable {
        display: inline-block;
        vertical-align: top;
        height: 100%;
        background-color: #DDDDDD;
    }

    .TableFrame_TimeTable_Columns {
        font-size: 16px;
        color: #888888;
        font-weight: bolder;
        white-space: nowrap;
        text-align: center;
        display: inline-block;
        vertical-align: top;
        height: 100%;
        box-sizing: border-box;
        border: 0.3px solid #BBBBBB;
    }

    .TableFrame_TimeTable_Columns_Dark {
        background-color: #D0D0D0;
    }

    .TableFrame_TimeTable_Columns_Light {
        background-color: #DDDDDD;
    }


</style>

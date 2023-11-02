<script setup>
import { provide, reactive, ref, readonly } from 'vue';
import ChoosePlatform from './components/ChoosePlatform.vue'

const view_date = reactive({'value':new Date( (new Date()).getFullYear(), (new Date()).getMonth(), (new Date()).getDate(), (new Date()).getHours(), (new Date()).getMinutes(), (new Date()).getSeconds() )})

const view_length = reactive({value:{
  position: 4, 
  options: ['full_year', 'half_year', 'three_months', 'two_months', 'month', 'three_weeks', 'two_weeks', 'week', 'five_days', 'three_days', 'day'],
  names:   ['一年',       '半年',      '季度',         '两月',        '一月',  '三周',        '两周',       '一周', '五天',       '三天',       '一天', '　　']
}})

const display_comment = ref(false)

const get_display_width = () => {
  return document.body.clientWidth-300
  // return document.querySelector('.TableFrame').clientWidth - document.querySelector('.TableFrame_Tasks').clientWidth
}

const get_length = () => {
    let opt = view_length.value.options[view_length.value.position]
    switch(opt){
        case 'full_year':
            return 365*24*60*60*1000
        case 'half_year':
            return 365*24*60*60*1000/2
        case 'three_months':
            return 90*24*60*60*1000
        case 'two_months':
            return 60*24*60*60*1000
        case 'month':
            return 30*24*60*60*1000
        case 'three_weeks':
            return 21*24*60*60*1000
        case 'two_weeks':
            return 14*24*60*60*1000
        case 'week':
            return 7*24*60*60*1000
        case 'five_days':
            return 5*24*60*60*1000
        case 'three_days':
            return 3*24*60*60*1000
        case 'day':
            return 24*60*60*1000
    }
}

const num2hanzi = (num) => {
  let l = ['零', '一', '二', '三', '四', '五', '六', '七', '八', '九', '十', '十一', '十二', '十三', '十四', '十五', '十六', '十七', '十八', '十九', '二十', '二十一', '二十二', '二十三', '二十四', '二十五', '二十六', '二十七', '二十八', '二十九', '三十', '三十一', '三十二']
  if (num>=0 && num <=32) return l[num]
  return '表里没有'
}

const task_data0 = {
    order:['123', '432'],
    content:{
        '123':{
            name:'123',
            start:1698817431154,
            end:1698827431154,
            comment:'this is 123',
            color:'#AABBCC',
            unfold:true,
            order:['c2', 'c1'],
            content:{
                'c1':{
                    name:'123_c1',
                    start:1698817454154,
                    end:1698820431154,
                    comment:'this is 123_c1'
                },
                'c2':{
                    name:'123_c2',
                    start:1698822431154,
                    end:1698825431154,
                    comment:'this is 123_c2'
                }
            }
        },
        '432':{
            name:'432',
            start:1698817431154,
            end:1698887431154,
            comment:'this is 432',
            color:'#FF0000',
            unfold:true,
            order:[],
            content:{}
        }
    }
}

const task_data = reactive({value:task_data0})

const change_data = (f) => {
    f(task_data.value)
}

provide('view_date', view_date)
provide('view_length', view_length)
provide('display_comment', display_comment)
provide('get_display_width', get_display_width)
provide('get_length', get_length)
provide('num2hanzi', num2hanzi)
provide('task_data', readonly(task_data)) // may have error, no error up to now
provide('change_data', change_data)


</script>

<template>
  <ChoosePlatform />
</template>

<style scoped>
</style>

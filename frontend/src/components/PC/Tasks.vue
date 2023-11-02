<script setup>

import { inject, ref, onMounted } from 'vue';
const task_data = inject('task_data')
const get_display_width = inject('get_display_width')
const get_length = inject('get_length')
const view_date = inject('view_date')
const display_comment = inject('display_comment')
const change_data = inject('change_data')

const width_culculator = (item) => {
    let start = item.start
    let end = item.end
    let total_length = get_length()
    let display_width = get_display_width()
    let begin_time = Number(view_date.value)
    if ( end < begin_time ) {
        return 0
    } else if ( start < begin_time ) {
        return (end-begin_time)/total_length*display_width
    } else if ( end < begin_time + total_length ) {
        return (end-start)/total_length*display_width
    } else if ( start < begin_time + total_length ) {
        return (begin_time + total_length - start)/total_length*display_width
    } else {
        return 0
    }
}

const offset_culculator = (item) => {
    let start = item.start
    let total_length = get_length()
    let display_width = get_display_width()
    let begin_time = Number(view_date.value)
    if ( start < begin_time || start > begin_time + total_length) {
        return 0
    } else {
        return (start-begin_time)/total_length*display_width
    }
}



const toggle_fold = (i) => {
    // alert(task_data.value.content[i].unfold)
    let f = (data) => { data.content[i].unfold = !data.content[i].unfold }
    change_data(f)
}

let handle_title_click_internal_timer = null
let handle_title_click_internal_timer_ended = true
const click_interval = 150
const handle_title_click = (i) => {
    // clear timer
    if ( handle_title_click_internal_timer ) { clearTimeout(handle_title_click_internal_timer) }
    // single click and double click
    if ( handle_title_click_internal_timer_ended ) {
        // single click
        handle_title_click_internal_timer = setTimeout( () => { 
            toggle_fold(i)
            handle_title_click_internal_timer_ended = true
        }, click_interval )
        handle_title_click_internal_timer_ended = false
    } else {
        // double click
        handle_title_click_internal_timer_ended = true
        alert("dblclick")
    }
    
}


const f = () => { alert('123') }

// force refresh when loading or resizing
const refresh_in_tasks = ref('refresh_the_div_in_tasks')
const refrashfunc_in_tasks = () => {refresh_in_tasks.value=''+Number(new Date())+'_in_tasks'}
onMounted( () => { refrashfunc_in_tasks() } )
window.addEventListener("resize", refrashfunc_in_tasks, false)

</script>

<template>
    <div class="Tasks" :key="refresh_in_tasks">
        <div v-for="i of task_data.value.order" class="Tasks_item">
            <div class="Tasks_item_parent">
                <div class="Tasks_item_title"
                    @click="handle_title_click(i)"
                    :style="{'border-left': '0.4em solid '+task_data.value.content[i].color}" >
                    {{ task_data.value.content[i].name }}
                </div>
                <div class="Tasks_item_bar"
                    :style="{'width':get_display_width()+'px'}">
                    <div class="Tasks_item_bar_whole"
                    :style="{
                        'width':width_culculator(task_data.value.content[i])+'px',
                        'background-color':task_data.value.content[i].color,
                        'transform':'translate(' + offset_culculator(task_data.value.content[i]) + 'px, 0px)'}"></div>
                    <div class="Tasks_item_bar_comment" v-if="display_comment">{{task_data.value.content[i].comment}}</div>
                </div>
            </div>
            <div v-for="j of task_data.value.content[i].order" class="Tasks_item_child"
                 v-if="task_data.value.content[i].unfold" >
                <div class="Tasks_item_title"
                    :style="{'border-left': '0.4em solid '+task_data.value.content[i].color}" >
                    {{ task_data.value.content[i].content[j].name }}
                </div>
                <div class="Tasks_item_bar"
                    :style="{'width':get_display_width()+'px'}">
                    <div class="Tasks_item_bar_whole"
                    :style="{
                        'width':width_culculator(task_data.value.content[i].content[j])+'px',
                        'background-color':task_data.value.content[i].color,
                        'transform':'translate(' + offset_culculator(task_data.value.content[i].content[j]) + 'px, 0px)'}"></div>
                    <div class="Tasks_item_bar_comment" v-if="display_comment">{{task_data.value.content[i].content[j].comment}}</div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
    .Tasks {
        font-size: 20px;
        position: absolute;
        user-select: none;
        overflow-x: hidden;
        /* background-color: rgba(0, 255, 0, 0.05); */
    }

    .Tasks_item_title {
        box-sizing: border-box;
        border-left: 0.4em solid #888888;
        width:300px;
        height: 2em;
        padding-top: 0.4em;
        text-align: center;
        display: inline-block;
        vertical-align: top;
        transition: 200ms;
    }

    .Tasks_item_bar {
        opacity: 70%;
        height: 2em;
        display: inline-block;
        vertical-align: top;
    }

    .Tasks_item_parent {
        white-space: nowrap;
        border-top: solid 1px #BBBBBB;
    }

    .Tasks_item_parent .Tasks_item_title:hover {
        background-color: #CCCCCC;
    }

    .Tasks_item_bar_comment {
        margin-top: 0.4em;
        position: absolute;
        transform: translate(20px, 0px);
        /* left: 400px; */
    }

    .Tasks_item_bar_whole {
        background-color: red;
        position: absolute;
        height: 2em;
    }

    .Tasks_item_child {
        overflow: hidden;
        transition: 200ms;
    }
</style>

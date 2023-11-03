<script setup>

import { inject, watch, nextTick } from 'vue';

const editing = inject('editing')
const edit_ID = inject('edit_ID')
const task_data = inject('task_data')
const Date2String = inject('Date2String')

const close_window = () => { editing.value = false }


// To make window can be dragged

watch(editing, async (new_value, old_value) => {
    // alert("0 old:"+old_value+' new:'+new_value)
    if (new_value) {
        await new Promise( (resolve, reject) => {
            let Head = null
            let timer = setInterval(()=>{
                console.log('trying get Editor_window_head element')
                Head = document.querySelector('.Editor_window_head')
                if (Head) {
                    let Editor = document.querySelector('.Editor')
                    let Editor_window = document.querySelector('.Editor_window')
                    let IsMouseDown = false
                    let Init_mouse_pos = [0, 0]
                    let Init_pos = [0, 0]

                    Head.addEventListener('mousedown', (e) => {
                        console.log(e)
                        IsMouseDown = true
                        Init_mouse_pos = [ e.clientX, e.clientY ]
                        Init_pos = [ Number(Editor_window.style.left.slice(0, -2)), Number(Editor_window.style.top.slice(0, -2)) ]
                    })

                    Editor.addEventListener('mousemove', (e) => {
                        if( IsMouseDown ) {
                            let New_mouse_pos = [ e.clientX, e.clientY ]
                            Editor_window.style.left = -Init_mouse_pos[0] + New_mouse_pos[0] + Init_pos[0] + 'px'
                            Editor_window.style.top  = -Init_mouse_pos[1] + New_mouse_pos[1] + Init_pos[1] + 'px'
                        }
                    })

                    Editor.addEventListener( 'mouseup', (e) => { IsMouseDown = false } )
                    resolve(0)
                    clearInterval(timer)
                }
            }, 200)
        } )
    }
    // alert("1 old:"+old_value+' new:'+new_value)
})

</script>

<template>
    <div class="Editor" v-if="editing">
        <div class="Editor_background" @dblclick="close_window"></div>
        <div class="Editor_window" :style="{'top':'80px', 'left':'80px'}">
            <div class="Editor_window_head">
                <div class="Editor_close_button"  @click="close_window"></div>
            </div>
            <div class="Editor_window_content">
                <div class="Editor_ID">任务ID: {{ edit_ID }}</div>
                <div class="Editor_name">任务名称: {{ task_data.value.content[edit_ID].name }}</div>
                <div class="Editor_time">持续时间: {{ Date2String(new Date(task_data.value.content[edit_ID].start)) }} 至 {{ Date2String(new Date(task_data.value.content[edit_ID].end)) }}</div>
            </div>
        </div>
    </div>
</template>

<style scoped>

.Editor {
    position: fixed;
    width: 100%;
    height: 100%;
}

.Editor_background {
    position: fixed;
    width: 100%;
    height: 100%;
    background-color: #00000033;
    z-index: -1;
}

.Editor_window {
    position: absolute;
    /* left: 0;
    top: 0; */
    /* left: 15%;
    top: 5em; */
    width: 70%;
    height: 60%;
    background-color: #FFFFFF;
}

.Editor_window_head {
    height: 40px;
    background-color: #DDDDDD;
}

.Editor_close_button {
    float: right;
    border: 1px solid #000000;
    background-color: #FFFFFF;
    border-radius: 50%;
    width: 30px;
    height: 30px;
    transition: 200ms;
}

.Editor_close_button:hover {
    transform: rotate(90deg) scale(1.2);
}

.Editor_close_button:before {
    content: '';
    display: block;
    position: absolute;
    width: 2px; 
    height: 20px;
    background-color: #000000;
    transform: matrix(0.7071, 0.7071, -0.7071, 0.7071, 14, 5);
}
.Editor_close_button:after {
    content: '';
    display: block;
    position: absolute;
    width: 2px; 
    height: 20px;
    background-color: #000000;
    transform: matrix(0.7071, -0.7071, 0.7071, 0.7071, 14, 5);
}

.Editor_window_content {
    background-color: #FF000033;
}

</style>

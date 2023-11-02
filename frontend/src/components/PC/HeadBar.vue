<script setup>
import { inject } from 'vue';
import { ref } from 'vue'


const Date2String = (date) => {
    let str = ''
    str += date.getFullYear()
    str += '-' + ( date.getMonth()+1 < 10 ? '0' : '' ) + (date.getMonth()+1)
    str += '-' + ( date.getDate() < 10 ? '0' : '' ) + date.getDate()
    // str += ' ' + date.getSeconds() // test response
    return str
}

// TODO: pace should change according to view length
const ChangeViewDay = (mode) => {
    // alert( view_date.value)
    if (mode === "LEFT") {
        view_date.value = new Date( view_date.value.getFullYear(), view_date.value.getMonth()-1, view_date.value.getDate() )
    } else if (mode === "left") {
        view_date.value = new Date( view_date.value.getFullYear(), view_date.value.getMonth(), view_date.value.getDate()-1 )
    } else if (mode === "right") {
        view_date.value = new Date( view_date.value.getFullYear(), view_date.value.getMonth(), view_date.value.getDate()+1 )
    } else if (mode === "RIGHT") {
        view_date.value = new Date( view_date.value.getFullYear(), view_date.value.getMonth()+1, view_date.value.getDate() )
    }
    viewday.value = Date2String(view_date.value)
}

const ChangeViewLength = (mode) => {
    if (mode === "LEFT" && view_length.value.position > 0) {
        view_length.value.position -= 1
    } else if (mode === "RIGHT" && view_length.value.position < view_length.value.options.length-1) {
        view_length.value.position += 1
    }
    view_option_left.value = view_length.value.position > 0 ? view_length.value.names[view_length.value.position-1] : view_length.value.names[view_length.value.options.length]
    view_option_center.value = view_length.value.names[view_length.value.position]
    view_option_right.value = view_length.value.names[view_length.value.position+1]
}

const ResetViewDay = () => {
    view_date.value = new Date()
    viewday.value = Date2String(view_date.value)
}

const ChangeCommentDisplay = () => {
    display_comment.value = !display_comment.value
}

const today = ref(Date2String(new Date()))
let today_timer = setInterval(()=>{
    today.value = Date2String(new Date())
}, 1000)

const view_date = inject('view_date')
const viewday = ref(Date2String(view_date.value))

const view_length = inject('view_length')
const view_option_left = ref( view_length.value.position > 0 ? view_length.value.names[view_length.value.position-1] : view_length.value.names[view_length.value.options.length] )
const view_option_center = ref( view_length.value.names[view_length.value.position] )
const view_option_right = ref( view_length.value.names[view_length.value.position+1] )

const display_comment = inject('display_comment')

</script>

<template>
    <div class="HeadBar">
        <div class="HeadBarItem HeadBar_today">
            <span @click="ResetViewDay()">今日：{{ today }}</span>
        </div>
        <div class="HeadBarItem HeadBar_viewday">
            <span class="HeadBar_viewday_changer" @click="ChangeViewDay('LEFT')">&nbsp;◀&nbsp;</span>
            <span class="HeadBar_viewday_changer" @click="ChangeViewDay('left')">&nbsp;&lt;&nbsp;</span>
            <span>{{ viewday }}</span>
            <span class="HeadBar_viewday_changer" @click="ChangeViewDay('right')">&nbsp;&gt;&nbsp;</span>
            <span class="HeadBar_viewday_changer" @click="ChangeViewDay('RIGHT')">&nbsp;▶&nbsp;</span>
        </div>
        <div class="HeadBarItem HeadBar_viewlength">
            <span class="HeadBar_viewlength_changer" @click="ChangeViewLength('LEFT')">&nbsp;&nbsp;{{ view_option_left }}&nbsp;&nbsp;</span>
            <span>{{ view_option_center }}</span>
            <span class="HeadBar_viewlength_changer" @click="ChangeViewLength('RIGHT')">&nbsp;&nbsp;{{ view_option_right }}&nbsp;&nbsp;</span>
        </div>
        <div class="HeadBarItem HeadBar_comment" 
            :class="display_comment ? 'HeadBar_comment_selected' : 'HeadBar_comment_unselected'"
            @click="ChangeCommentDisplay">
            <span>&nbsp;&nbsp;备注&nbsp;&nbsp;</span>
        </div>
    </div>

    <div class="HeadBarPlaceHolder">
        <div class="HeadBarItem HeadBar_today">
            <span>今日：2023-10-23</span>
        </div>
        <div class="HeadBarItem HeadBar_viewday">
            <span class="HeadBar_viewday_changer">&nbsp;◀&nbsp;</span>
            <span class="HeadBar_viewday_changer">&nbsp;&lt;&nbsp;</span>
            <span>2023-10-23</span>
            <span class="HeadBar_viewday_changer">&nbsp;&gt;&nbsp;</span>
            <span class="HeadBar_viewday_changer">&nbsp;▶&nbsp;</span>
        </div>
        <div class="HeadBarItem HeadBar_viewlength">
            <span class="HeadBar_viewlength_changer">&nbsp;&nbsp;今天&nbsp;&nbsp;</span>
            <span>今天</span>
            <span class="HeadBar_viewlength_changer">&nbsp;&nbsp;今天&nbsp;&nbsp;</span>
        </div>
        <div class="HeadBarItem HeadBar_comment">
            <span>&nbsp;&nbsp;备注&nbsp;&nbsp;</span>
        </div>
    </div>
</template>

<style scoped>
    .HeadBar {
        width: 100%;
        background-color: #FFFFFF;
        box-shadow: 0px 5px 5px #DDDDDD;
        padding: 8px;
        font-size: 20px;
        position:fixed;
        z-index: 1;
    }
    
    .HeadBarPlaceHolder {
        width: 100%;
        padding: 8px;
        font-size: 20px;
    }
    
    .HeadBarItem {
        display: inline-block;
        background-color: #EEEEEE;
        padding: 4px;
        border-radius: 4px;
        -webkit-user-select: none;
        user-select: none;
        transition: 200ms;
        margin-left: 10px;
    }

    .HeadBar_today {
        margin-left: 0px;
    }

    .HeadBar_today:hover {
        background-color: #CCCCCC;
    }

    .HeadBar_viewday_changer {
        border-radius: 4px;
        transition: 200ms;
    }

    .HeadBar_viewday_changer:hover {
        background-color: #CCCCCC;
    }


    .HeadBar_viewlength_changer {
        border-radius: 4px;
        transition: 200ms;
        color: rgba(0, 0, 0, 0.365);
    }

    .HeadBar_viewlength_changer:hover {
        background-color: #CCCCCC;
    }

    .HeadBar_comment_changer {
        border-radius: 4px;
        transition: 200ms;
    }

    .HeadBar_comment:hover {
        background-color: #CCCCCC;
    }

    .HeadBar_comment_selected {
        background-color: #888888;
        color: #FFFFFF;
    }

</style>

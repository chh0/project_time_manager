<script setup>
import { ref } from 'vue'
import PC from './PC/PC.vue';

// 判断是电脑还是移动端横屏还是移动端竖屏
function change_screen_type() {
    let type='PC'
    if (window.orientation == 90 || window.orientation == -90) { //竖屏 vertical
        type = "vertical"
    } else if (window.orientation == 0 || window.orientation == 180) { //横屏 horizontal
        type = "horizontal"
    } else {
        type = "PC"
    }
    screen_type.value = type
}
let screen_type = ref('PC')
change_screen_type()
window.addEventListener("resize", change_screen_type, false)

</script>

<template>
    <div v-if="screen_type=='PC'"><PC /></div>
    <div v-if="screen_type=='vertical'">222</div>
    <div v-if="screen_type=='horizontal'">333</div>
</template>

<style scoped>
</style>

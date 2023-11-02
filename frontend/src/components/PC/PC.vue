<script setup>

import HeadBar from './HeadBar.vue';
import TableFrame from './TableFrame.vue'
import Tasks from './Tasks.vue';

</script>

<template>
    <div class="PC">
        <HeadBar />
        <TableFrame />
        <Tasks />
    </div>
</template>

<style scoped>

.PC {
    overflow-x: hidden; /* hide stroll bar in x direction */
}

</style>

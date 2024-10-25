<script setup>
/* eslint-disable */
defineProps(['content', 'img'])
</script>
<template>
    <div class="card">
        <p>{{ content }}</p>
        <img :src="'data:image/png;base64, ' + img">
    </div>
</template>

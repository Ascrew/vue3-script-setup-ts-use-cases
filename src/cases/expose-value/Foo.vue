<template>
    <bar ref="barRef"></bar>
</template>
<script setup lang="ts">
import { ref } from 'vue';
import Bar, { IExposeType } from './Bar.vue';
const barRef = ref<IExposeType>();
const a = barRef.value?.a  // number | undefined
const b = barRef.value?.b  // Ref<number> | undefined
</script>
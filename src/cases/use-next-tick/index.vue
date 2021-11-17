<template>
    <div></div>
</template>
<script setup lang="ts">
import { nextTick } from 'vue';

nextTick(() => {
    // ...
})

// 还可以使用 async/await
async () => {
    await nextTick()
    // ....
}
</script>
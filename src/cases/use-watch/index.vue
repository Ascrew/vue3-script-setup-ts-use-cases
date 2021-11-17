<template>
    <div>{{ numberRef }}</div>
</template>
<script setup lang="ts">
import { ref, watch, watchEffect } from 'vue';
const numberRef = ref(0) // ===> Ref<number>
interface IFoo {
    bar: string
}
const fooRef = ref<IFoo>() // ===> Ref<IFoo | undefined>

watchEffect(() => console.log(fooRef.value?.bar))
watch(numberRef, () => {
    console.log(`numberRef变化了`)
})
const stop = watch(fooRef, () => {
    console.log(`fooRef变化了`)
}, {
    deep: true
}) // 检查深度嵌套的对象或数组
stop(); // 停止侦听
</script>
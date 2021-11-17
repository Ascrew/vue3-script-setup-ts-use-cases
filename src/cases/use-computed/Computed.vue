<template>
    <div>{{ fooComputed?.bar }}</div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue';
const numberRef = ref(0) // ===> Ref<number>
const numberComputed = computed(() => numberRef.value) // ===> ComputedRef<number>
interface IFoo {
    bar: string
}
const fooRef = ref<IFoo>() // ===> Ref<IFoo | undefined>
const fooComputed = computed(() => {
    return fooRef.value
}) // ===> ComputedRef<IFoo | undefined>
</script>
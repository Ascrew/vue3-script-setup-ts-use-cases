<template>
    <div>{{ fooComputedWritable?.bar }}</div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue';
interface IFoo {
    bar: string
}
const fooRef = ref<IFoo>() // ===> Ref<IFoo | undefined>
const fooComputedWritable = computed({
    get: () => {
        return fooRef.value
    },
    set: (value) => {
        fooRef.value = value
    }
}) // ===> WritableComputedRef<IFoo | undefined>
</script>
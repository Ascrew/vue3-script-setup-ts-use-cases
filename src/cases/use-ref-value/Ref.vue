<template>
    <div>{{ numberRef }}</div>
</template>
<script setup lang="ts">
import { ref } from 'vue';
const numberRef = ref(0) // ===> Ref<number>
const stringRef = ref("") // ===> Ref<string>
interface IFoo {
    bar: string
}
const fooRef = ref<IFoo>() // ===> Ref<IFoo | undefined>
</script>
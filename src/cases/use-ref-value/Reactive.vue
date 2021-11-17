<template>
    <div>{{ book1.bar }}</div>
</template>
<script setup lang="ts">
import { reactive } from 'vue';
interface IFoo {
    bar: string
}
// 第一种
const book1 = reactive<IFoo>({ bar: 'bar' })
// 第二种
const book2: IFoo = reactive({ bar: 'bar' })
// 第三种
const book3 = reactive({ bar: 'bar' }) as IFoo
</script>
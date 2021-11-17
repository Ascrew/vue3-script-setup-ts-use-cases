<template>
    <div></div>
</template>
<script setup lang="ts">
import { provide } from 'vue';
export interface IUser {
    name: string,
    age: number
}
provide("name", "foo")
provide<IUser>("user", {
    name: "foo",
    age: 23
})
</script>
<template>
    <Foo></Foo>
    <foo-item></foo-item>
    <component :is="Foo" />
    <component :is="someCondition ? Foo : FooItem" />
</template>
<script setup lang="ts">
import Foo from "./Foo.vue"
import FooItem from "./FooItem.vue"
const someCondition = false
</script>
// FooBar.vue
<template>
    <div>
        <!-- 一个单文件组件可以通过它的文件名被其自己所引用 -->
        <FooBar></FooBar>
        <foo-bar></foo-bar>

        <foo-bar-other></foo-bar-other>
    </div>
</template>
<script setup lang="ts">
// 使用 import 别名导入避免冲突
import { default as FooBarOther } from './others/FooBar.vue'
</script>
<template>
    <div></div>
</template>
<script setup lang="ts">
import { inject } from 'vue';
import { IUser } from './Foo.vue';

const name = inject<string>("name") // ===> string | undefined
const user = inject<IUser>("user") // ===> IUser | undefined
</script>
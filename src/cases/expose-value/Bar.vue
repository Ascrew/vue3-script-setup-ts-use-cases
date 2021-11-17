<template>
  <div></div>
</template>
<script setup lang="ts">
import { ref } from 'vue'
const a = 1
const b = ref(2)

const exposeValue = {
  a,
  b
}
defineExpose(exposeValue)
export type IExposeType = typeof exposeValue
</script>
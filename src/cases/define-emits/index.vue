<template>
    <div @click="emitsWithObject('bar', $event)"></div>
</template>
<script setup lang="ts">
interface IUserInput {
    email: string,
    password: string
}
// 数组方式
const emitsWithArray = defineEmits(["foo", "bar"])
// 对象方式
const emitsWithObject = defineEmits({
    // 带有验证函数，并且指定负载类型
    foo: (payload: IUserInput) => {
        if (payload.email && payload.password) {
            return true
        } else {
            console.warn(`Invalid submit event payload!`)
            return false
        }
    },
    // 仅指定负载类型
    bar: (evevnt: MouseEvent) => true
})
// 泛型方式
const emitsWithGeneric = defineEmits<{
    (e: 'foo', id: number): void
    (e: 'bar', value: string): void
}>()

// 触发事件
emitsWithArray("foo")
emitsWithObject("foo", { email: "bar@aa.com", password: 'bp' })
emitsWithGeneric("foo", 1)
</script>
<template>
    <div>
        <!-- 直接使用即可，不需要toRefs转换 -->
        {{ foo }}
    </div>
</template>
<script setup lang="ts">
import { toRefs } from 'vue';
interface ICustomType {
    foo: string,
    bar: string
}
const props = defineProps({
    foo: String, // 使用构造函数声明类型
    fooMultiTypes: [String, Number], // 多个类型
    fooCustomType: Object as () => ICustomType, // 自定义类型
    fooCustomTypeWithRequire: {
        type: Object as () => ICustomType,
        required: true
    }, // 自定义类型，必选
    fooCustomTypeWithDefault: {
        type: Object as () => ICustomType,
        default: () => {
            return {
                foo: "foo",
                bar: "bar"
            }
        }
    }, // 自定义类型，带默认值
})

// 1. 可以在模板<template>中使用声明的props，不需要用toRefs转换
// 2. 如果某一个值需要在setup中使用，则需要用toRefs转换下，然后把它解构出来
const {
    foo,  // ===> Ref<string | undefined> | undefined
    fooMultiTypes, // ===> Ref<string | number | undefined> | undefined
    fooCustomType, // ===> Ref<ICustomType | undefined> | undefined
    fooCustomTypeWithRequire, // ===> Ref<ICustomType>
} = toRefs(props)
</script>
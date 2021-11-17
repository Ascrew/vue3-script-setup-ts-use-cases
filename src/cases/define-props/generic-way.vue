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
const props = defineProps<{
    foo?: string,
    fooWithRequire: string,
    fooMultiTypes: string | number,
    fooCustomType?: ICustomType,
    fooCustomTypeWithRequire: ICustomType
}>()

// 泛型方式声明默认值，需要使用withDefaults 编译器宏
const propsWithDefault = withDefaults(
    defineProps<{
        fooCustomTypeWithDefault: ICustomType
    }>(),
    {
        fooCustomTypeWithDefault: () => {
            return {
                foo: "foo",
                bar: "bar"
            }
        }
    })

// 1. 可以在模板<template>中使用声明的props，不需要用toRefs转换
// 2. 如果某一个值需要在setup中使用，则需要用toRefs转换下，然后把它解构出来
const {
    foo,  // ===> Ref<string | undefined> | undefined
    fooWithRequire,  // ===> Ref<string>
    fooMultiTypes, // ===> Ref<string | number>
    fooCustomType, // ===> Ref<ICustomType | undefined> | undefined
    fooCustomTypeWithRequire, // ===> Ref<ICustomType>
} = toRefs(props)

const {
    fooCustomTypeWithDefault,  // ===> Ref<ICustomType>
} = toRefs(propsWithDefault)
</script>
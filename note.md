# Vue3中 `<script setup lang="ts">` 使用总结

Vue3.2发布时，`<script setup>`语法糖也跟着正式发布了，结合`typescript`来使用的话，书写Vue组件的体验比之前要舒服太多了。本文总结了一些在`<script setup lang="ts">`中常用的书写方式，希望可以为你在使用`<script setup lang="ts">`的时候提供帮助。

## 开发环境搭建

- 推荐使用vite来快速初始化一个Vue3+Typescript项目，它会配置好一些开箱即用的配置
- 推荐使用vscode，并且安装volar插件辅助开发
- 推荐自定义一个代码片段，辅助开发
  ```json
  // vue.json
  {
	"vue setup ts less": {
		"prefix": "vstl",
		"body": [
			"<template>",
			"    <div></div>",
			"</template>",
			"<script setup lang=\"ts\">",
			"    ",
			"</script>",
			"<style lang=\"less\" scoped>",
			"</style>",
		],
		"description": "vue3 setup ts less"
	}
  }
  ```
## 使用响应式变量

在`<script setup lang="ts">`中定义的响应式变量默认都是暴露给模板的

- 使用`ref`：`ref`会对传入的初始值做类型推导，如果是复杂的类型，还可以通过泛型参数来指定
```html
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
```
- 使用`reactive`：Vue文档介绍了三种方式来为`reactive`声明类型
```html
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
```
- 使用`computed`
- 使用`watch`

```html
<template>
    <div>{{ numberRef }}</div>
</template>
<script setup lang="ts">
import { computed, reactive, ref, watch } from 'vue';
// ref
const numberRef = ref(0) // ===> Ref<number>
const stringRef = ref("") // ===> Ref<string>
interface IFoo {
    bar: string
}
const fooRef = ref<IFoo>() // ===> Ref<IFoo | undefined>
// reactive
const fooReactive: IFoo = reactive({
    bar: ""
})
// computed
const fooComputed = computed(() => {
    return fooRef.value
}) // ===> ComputedRef<IFoo | undefined>
const fooComputedWritable = computed({
    get: () => {
        return fooRef.value
    },
    set: (value) => {
        fooRef.value = value
    }
}) // ===> WritableComputedRef<IFoo | undefined>
// watch
watch(fooRef, () => {
    console.log(`fooRef变化了`)
})
watch(fooRef, () => {
    console.log(`fooRef变化了`)
}, {
    deep: true
}) // 检查深度嵌套的对象或数组
</script>
```
## 使用`computed`
`computed`方法会自动推导出类型
- 基础使用方式
```html
<template>
    <div>{{ fooComputed?.bar }}</div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue';
const numberRef = ref(0) // ===> Ref<number>
const numberComputed = computed(() => numberRef.value) // ===> ComputedRef<number>
interface IFoo {
    bar: string
}
const fooRef = ref<IFoo>() // ===> Ref<IFoo | undefined>
const fooComputed = computed(() => {
    return fooRef.value
}) // ===> ComputedRef<IFoo | undefined>
</script>
```
- 可写的`computed`
```html
<template>
    <div>{{ fooComputedWritable?.bar }}</div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue';
interface IFoo {
    bar: string
}
const fooRef = ref<IFoo>() // ===> Ref<IFoo | undefined>
const fooComputedWritable = computed({
    get: () => {
        return fooRef.value
    },
    set: (value) => {
        fooRef.value = value
    }
}) // ===> WritableComputedRef<IFoo | undefined>
</script>
```
## 使用`watch`和`watchEffect`
直接引入`watch`和`watchEffect`方法就可以使用了
```html
<template>
    <div>{{ numberRef }}</div>
</template>
<script setup lang="ts">
import { ref, watch, watchEffect } from 'vue';
const numberRef = ref(0) // ===> Ref<number>
interface IFoo {
    bar: string
}
const fooRef = ref<IFoo>() // ===> Ref<IFoo | undefined>

watchEffect(() => console.log(fooRef.value?.bar))
watch(numberRef, () => {
    console.log(`numberRef变化了`)
})
const stop = watch(fooRef, () => {
    console.log(`fooRef变化了`)
}, {
    deep: true
}) // 检查深度嵌套的对象或数组
stop(); // 停止侦听
</script>
</script>
```
## 使用`nextTick`
直接引入`nextTick`方法使用
```html
<template>
    <div></div>
</template>
<script setup lang="ts">
import { nextTick } from 'vue';

nextTick(() => {
    // ...
})

// 还可以使用 async/await
async () => {
    await nextTick()
    // ....
}
</script>
```
## 使用其他组件
直接引入其他的组件，然后在模板中使用就可以了，引入的组件会自动注册的。需要注意的是，使用动态组件的时候，应该使用动态的 `:is` 来绑定
```html
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
```
## 声明props

使用`defineProps`来声明`props`，分为不使用泛型的方式和使用泛型的方式，两者都可以同时声明类型和默认值

- 非泛型方式：此时它接收和 `props` 选项相同的值。
```html
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
```
- 泛型方式：此时声明默认值，需要 `withDefaults` 编译器宏
```html
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
```
## 声明emits
使用`defineEmits`来声明，它接收和 `emits` 选项相同的值。
```html
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
```
## 递归组件
一个单文件组件可以通过它的文件名被其自己所引用，但是在有命名冲入的情况下，需要使用import 别名导入来避免冲突
```html
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
```
## 生命周期钩子使用
直接引入对应的生命周期钩子函数使用即可
```html
<template>
    <div></div>
</template>
<script setup lang="ts">
import {
    onBeforeMount,
    onMounted,
    onBeforeUpdate,
    onUpdated,
    onBeforeUnmount,
    onUnmounted,
    onErrorCaptured,
    onRenderTracked,
    onRenderTriggered,
    onActivated,
    onDeactivated
} from "vue"

// 直接使用就好了
onMounted(() => {
    // ...
})
</script>
```
## 暴露组件属性
`<script setup>`中的变量默认是不会暴露出给其他组件的，使用`defineExpose`可以暴露需要其他组件可以访问的属性
- 在`Bar.vue`中暴露属性，由于使用`defineExpose`暴露的属性类型暂时还没法被`InstanceType<typeof Bar>`这种方式提取出来有，具体的可以参照这个[#4397](https://github.com/vuejs/vue-next/issues/4397)，所以需要使用`export`手动的将暴露的类型导出。
```html
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
```
- 在`Foo.vue`中通过`模板引用`使用暴露的属性
```html
<template>
    <bar ref="barRef"></bar>
</template>
<script setup lang="ts">
import { ref } from 'vue';
import Bar, { IExposeType } from './Bar.vue';
const barRef = ref<IExposeType>();
const a = barRef.value?.a  // number | undefined
const b = barRef.value?.b  // Ref<number> | undefined
</script>
```
## 使用provide和inject
`provide`和`inject`支持通过泛型来控制注入的变量的类型

- 在`Foo.vue`中使用`provide`提供需要注入的依赖
```html
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
```
- 在`Bar.vue`中使用`inject`引入注入的依赖
```html
<template>
    <div></div>
</template>
<script setup lang="ts">
import { inject } from 'vue';
import { IUser } from './Foo.vue';

const name = inject<string>("name") // ===> string | undefined
const user = inject<IUser>("user") // ===> IUser | undefined
</script>
```
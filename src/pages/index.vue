<script setup lang="ts">
import format from 'html-format'
import { data } from '~/composables/data'
const input = ref(data)
const copyIndex = ref(-1)
const newValue = computed(() => {
  if (!input.value)
    return ''
  const reg = /www.*?_[0-9]\n/g
  const tab: string[] = input.value.toString().match(reg)
  // .map(el => el.replace('\n', ''))
  const array = input.value.toString().split('\n').filter(Boolean)
  const newData: { title: string; script: string }[] = []
  const typeIndex = tab.map((el) => {
    return array.findIndex(item => item === el.replace('\n', ''))
  })
  typeIndex.reduce((acc, idnex) => {
    newData.push({
      title: array[acc],
      script: array.slice(acc + 1, idnex).join(''),
    })
    return acc = idnex
  }, 0)
  const len = typeIndex.length
  newData.push({
    title: array[typeIndex[len - 1]],
    script: array.slice(typeIndex[len - 1] + 1).join(''),
  })
  return newData.filter(item => item.script)
})
enum TypeName {
  dp = '详情',
  hp = '首页',
  anchor = '底部悬浮',
  cp = '320x480',
  cpt = '插屏广告',
}
const DP = 'dp'
const ANCHOR = 'anchor'
function formatTile(title: string) {
  const array = title.split('_')
  return `${array.find(el => el === DP) ? TypeName.dp : TypeName.hp}_${formatType(array)}`
}
function formatType(array: string[]): string {
  const str = []
  if (array.at(-2) === ANCHOR)
    str.push(TypeName.anchor)
  if (array.includes(TypeName.cp))
    str.push(TypeName.cpt)
  str.push(array.at(-1))
  return `${str.join('_')}`
}
function formatScript(script: string) {
  console.log(format(script))

  return format(script)
}
function copy(script: string, index: number) {
  const aux = document.createElement('input')

  // 设置元素内容
  aux.setAttribute('value', script)

  // 将元素插入页面进行调用
  document.body.appendChild(aux)

  // 复制内容
  aux.select()

  // 将内容复制到剪贴板
  document.execCommand('copy')

  // 删除创建元素
  document.body.removeChild(aux)

  // 提示
  copyIndex.value = index
}
</script>

<template>
  <div flex="~ ">
    <textarea v-model="input" name="" cols="30" rows="10" p-xy w100vh border b-rd />
    <div flex="1" ml1 :class="[newValue ? '' : 'border']">
      <ul>
        <li v-for="item, index in newValue" :key="index" border mb4 pxy b-rd>
          <h2 text-left font-600>
            <span color-green>{{ formatTile(item.title) }}</span>
            <div class="icon-btn mx-2" float-right title="copy">
              <div i-carbon-copy :class="[index === copyIndex ? 'color-green' : '']" @click="copy(item.script, index)" />
            </div>
          </h2>
          <p>{{ formatScript(item.script) }}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<route lang="yaml">
meta:
  layout: home
</route>

<style>
.dark textarea {
  color: aliceblue !important;
  background: #111;
}
</style>

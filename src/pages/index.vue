<script setup lang="ts">
import { data } from '~/composables/data'
enum TypeName {
  dp = '详情',
  hp = '首页',
  anchor = '底部悬浮',
  cp = '320x480',
  cpt = '插屏广告',
}
const DP = 'dp'
const ANCHOR = 'anchor'
const input = ref(data)
const copyIndex = ref(-1)
const newValue = computed(() => {
  if (!input.value)
    return ''
  const titleReg = /www.*?_[0-9]\n/g
  const scriptReg = /<script\b[^>]*><\/script>/
  const tab: string[] = input.value.toString().match(titleReg)
  // .map(el => el.replace('\n', ''))
  const array = input.value.toString().split('\n').filter(Boolean)
  const newData: { title: string; script: string }[] = []
  const typeIndex = tab.map((el) => {
    return array.findIndex(item => item === el.replace('\n', ''))
  })

  ;(function () {
    const len = typeIndex.length
    const lastData = array.slice(typeIndex[len - 1] + 1).join('')
    const headScript = lastData.match(scriptReg)[0]
    newData.push({
      title: 'head',
      script: headScript,
    },
    {
      title: `${DP}_head`,
      script: headScript,
    }, {
      title: array[typeIndex[len - 1]],
      script: lastData.replace(scriptReg, ''),
    },
    )
    typeIndex.reduce((acc, idnex) => {
      newData.push({
        title: array[acc],
        script: array.slice(acc + 1, idnex).join('').replace(scriptReg, ''),
      })
      return acc = idnex
    }, 0)
  }())
  return newData.filter(item => item.script)
})

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
function copy(script: string, index: number) {
  const aux = document.createElement('input')
  aux.setAttribute('value', script)
  document.body.appendChild(aux)
  aux.select()
  document.execCommand('copy')
  document.body.removeChild(aux)
  copyIndex.value = index
}
</script>

<template>
  <div flex="~ ">
    <textarea v-model="input" name="" cols="30" rows="10" p-xy w100vh border b-rd />
    <div flex="1" ml1 :class="[newValue ? '' : 'border']">
      <ul>
        <li v-for="item, index in newValue" :key="index" border mb4 pxy b-rd hover="bg-#CDF0EA/700" dark:hover="bg-#111/700">
          <h2 text-left font-600>
            <span bg="#FFF89A" dark:color-black border inline-block w-7 h-7 b-rd-7 lh-7 mr-2 font-600 text-center>{{ index + 1 }}</span><span color-green>{{ formatTile(item.title) }}</span>
            <div class="icon-btn mx-2" float-right title="copy">
              <div i-carbon-copy :class="[index === copyIndex ? 'color-green' : '']" @click="copy(item.script, index)" />
            </div>
          </h2>
          <p hover="color-red/700 font-600" dark:hover="color-yellow/700 font-600">
            {{ item.script }}
          </p>
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

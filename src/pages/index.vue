<script setup lang="ts">
const input = ref('')
const newValue = computed(() => {
  const reg = /www.*?_[0-9]\r/g
  const tab = input.value.match(reg).map(el => el.replace('\r', ''))
  const array = input.value.replaceAll('\r', '').split('\n').filter(Boolean)
  console.log(tab)
  console.log(array)
  const newData = []
  const idnexs = tab.map((el) => {
    return array.findIndex(item => item == el)
  })
  console.log(idnexs)
  idnexs.reduce((acc, idnex) => {
    newData.push({
      name: array[acc],
      script: array.slice(acc + 1, idnex).join(''),
    })
    return acc = idnex
  }, 0)
  const len = idnexs.length
  newData.push({
    name: array[idnexs[len - 1]],
    script: array.slice(idnexs[len - 1]).join(''),
  })
  return newData.filter(item => item.script)
})
</script>

<template>
  <div>
    <!-- <input id="" type="file" name=""> -->
    <textarea v-model="input" name="" cols="30" rows="10" w100vh border />
  </div>
</template>

<route lang="yaml">
meta:
  layout: home
</route>

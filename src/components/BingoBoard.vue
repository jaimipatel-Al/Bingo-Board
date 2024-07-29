<script setup>
import { onMounted, ref, watch } from 'vue'

const max = 100
const newNumber = ref()
const boxNumber = ref([])
const totalNumber = ref([])
const isStart = ref(false)
const isWin = ref(false)
const pickedNumbersList = ref([])

// set box number
const setBoxNumber = () => {
  const totalNumber = getTotalNumber()
  const arr = []

  while (arr.length < 25) {
    const indexOf = Math.floor(Math.random() * max)

    if (totalNumber[indexOf] !== -1) {
      arr.push(totalNumber[indexOf])
      totalNumber[indexOf] = -1
    }
  }

  boxNumber.value = arr
}

// total number 0-99
const getTotalNumber = () => {
  const totalNumber = []
  for (let i = 0; i < max; i++) {
    totalNumber.push(i)
  }
  return totalNumber
}

// start game
const start = () => {
  newNumber.value = Math.floor(Math.random() * totalNumber.value.length)
  const pickedNumber = totalNumber.value[newNumber.value]
  totalNumber.value = totalNumber.value.filter((num) => num !== pickedNumber)
  pickedNumbersList.value.push(pickedNumber)

  setTimeout(() => {
    if (totalNumber.value.length && isStart.value) start()
  }, 400)
}

// reset game
const reset = () => {
  setBoxNumber()
  totalNumber.value = getTotalNumber()
  isStart.value = false
  isWin.value = false
  pickedNumbersList.value = []
  newNumber.value = null
}

// show color in box
const checkNumberExist = (val) => {
  return pickedNumbersList.value.findIndex((e) => e == val) != -1
}

// show winner message and stop loop
watch(
  () => totalNumber?.value,
  (val) => {
    if (
      (isStart.value && val?.length == 0) ||
      boxNumber.value.every((item) => !val.includes(item))
    ) {
      newNumber.value = null
      isWin.value = true
      isStart.value = false
    }
  }
)

onMounted(() => {
  setBoxNumber()
  totalNumber.value = getTotalNumber()
})
</script>

<template>
  <div>
    <h1>Bingo Board</h1>
    <h2 v-if="isStart">{{ newNumber }}</h2>
    <div class="box">
      <div v-for="i in boxNumber" :key="i" :class="['number', checkNumberExist(i) ? 'active' : '']">
        {{ i }}
      </div>
    </div>

    <!-- action button  -->
    <div>
      <button
        v-if="!isWin"
        :disabled="isStart && totalNumber.length"
        @click="(isStart = true), start()"
      >
        Start
      </button>
      <button @click="reset">Refresh</button>
    </div>

    <!-- win -->
    <div v-if="isWin" class="win">You are winner!</div>
  </div>
</template>

<style>
.box {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  margin: 20px auto;
  width: fit-content;
}
.number {
  width: 60px;
  height: 60px;
  align-items: center;
  display: flex;
  justify-content: center;
  border: 1px solid #d0d0d0;
  font-size: 20px;
  font-weight: 700;
  color: #4c4c4c;
}
.number.active {
  background-color: rgb(0, 247, 255);
}
button {
  padding: 16px 20px;
  font-size: 12px;
  font-weight: 700;
  margin: 0 12px;
  border-radius: 12px;
  border: none;
  outline: none;
  box-shadow: 1px 1px 8px #ccc;
  cursor: pointer;
}
.win {
  padding: 20px;
  margin: 20px auto;
  color: rgb(9, 188, 39);
  font-weight: 700;
  font-size: 20px;
}
</style>

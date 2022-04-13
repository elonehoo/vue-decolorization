<script setup lang="ts">
import {ref} from 'vue'
import _ from 'underscore'

const isOver = ref(false)
const isSuccess = ref(false)
const step = ref(0)
const blockCount = ref(10)
const colors = ref(['red', 'yellow', 'blue', 'green'])
const blockColors = ref()
const selectedColor = ref()
const needCleanBlock = ref()

function blockSize(){
   return (1 / blockCount.value) * 100 + '%'
}


function clearBlock(color:any){
  if (color === blockColors.value[0]) {
    return
  }
  step.value = step.value - 1
  selectedColor.value = color
  getNeedCleanBlock(0)
  changeSelectColor()
}

function changeSelectColor(){
  var blockColors2 = Object.assign({}, blockColors.value)
  for (const key of needCleanBlock.value) {
    blockColors2[key] = selectedColor.value
  }
  blockColors.value = blockColors2
  checkGameStatus()
}

function getNeedCleanBlock(index:any){
  needCleanBlock.value = [index]
  let blockStark = [index]
  do {
    let surroundBlocks = getSurroundBlock(blockStark.pop())
    for (let blockIndex of surroundBlocks) {
      if (_.indexOf(needCleanBlock.value, blockIndex) === -1) {
        needCleanBlock.value.push(blockIndex)
        blockStark.push(blockIndex)
      }
    }
  } while (blockStark.length > 0)
}

function getSurroundBlock(index:any){
  let color = blockColors.value[index]
  let surroundBlock = []

  if (index % blockCount.value !== blockCount.value - 1 && _.indexOf(needCleanBlock.value, index + 1) === -1 && color === blockColors.value[index + 1]) {
    surroundBlock.push(index + 1)
  }
  if (index % blockCount.value !== 0 && _.indexOf(needCleanBlock.value, index - 1) === -1 && color === blockColors.value[index - 1]) {
        surroundBlock.push(index - 1)
  }
  if (_.indexOf(needCleanBlock.value, index + blockCount.value) === -1 && color === blockColors.value[index + blockCount.value]) {
        surroundBlock.push(index + blockCount.value)
  }
  if (_.indexOf(needCleanBlock.value, index - blockCount.value) === -1 && color === blockColors.value[index - blockCount.value]) {
        surroundBlock.push(index - blockCount.value)
  }
  return surroundBlock
}

function selectSize (size:any) {
  blockCount.value = size
  init()
}

function checkGameStatus () {
  let colors = _.groupBy(blockColors.value)
  if (_.size(colors) === 1) {
    isSuccess.value = true
    return
  }
  if (step.value <= 0) {
    isOver.value = true
  }
}

function nextPass () {
  selectSize(blockCount.value + 10)
}

function init () {
  isOver.value = false
  isSuccess.value = false
  step.value = blockCount.value * 1.2
  let blockColors2 = []
  for (let w = 0; w <= blockCount.value * blockCount.value; w++) {
    blockColors2[w] = _.sample(colors.value)
  }
  blockColors.value = blockColors2
}

init()

</script>

<template>
  <div class="color-container">
    <div class="size-wrap">
      <div>Choose a level</div>
      <div @click="selectSize(size * 10)" :class="'size-' + (size + 1)" v-for="size in 5" :key="size">{{size}}</div>
    </div>

    <div class="step-wrap">
      <div>current level</div>
      <div class="step">{{blockCount / 10}}</div>
      <div>steps</div>
      <div class="step">{{step}}</div>
    </div>

    <div class="game-panel">
      <div v-for="(index, col) in (blockCount * blockCount)" :key="col" :style="{'width': blockSize(),'height':blockSize()}">
        <div class="col-item" :class="blockColors[index - 1]"></div>
      </div>
    </div>
    <div class="color-btn-wrap">
      <div v-for="color in colors" :key="color" class="color-btn" :class="color" @click="clearBlock(color)"></div>
    </div>

    <div class="rule-wrap">
      <div>game rules:</div>
      <div>Click the color block below, take the upper left corner as the origin, and gradually swallow the adjacent colors until all the colors are unified</div>
    </div>

    <div v-show="isSuccess" class="success-panel">
      <div class="success-text">Successfully</div>
      <span @click="nextPass" class="next-pass">Next</span>
    </div>

    <div v-show="isOver" class="over-panel">
      <div class="over-text">Game Over</div>
      <span @click="init" class="restart">Again</span>
    </div>
  </div>
</template>

<style scoped>
.color-container {
  margin: auto;
  max-width: 380px;
}
.size-wrap {
  padding: 15px 15px 0;
  display: flex;
  justify-content: space-around;
  color: #333;
  font-weight: 600;
}
.size-wrap .size-1 {
  color: #eee;
}
.size-wrap .size-2 {
  color: #ddd;
}
.size-wrap .size-3 {
  color: #ccc;
}
.size-wrap .size-4 {
  color: #bbb;
}
.size-wrap .size-5 {
  color: #aaa;
}
.step-wrap {
  padding-top: 20px;
  display: flex;
  align-items: center;
  justify-content: space-around;
  justify-content: center;
}
.step-wrap .step {
  margin: 0 10px;
  font-weight: 600;
  color: #555;
}
.game-panel {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  margin: 20px auto;
  position: relative;
  width: 90vw;
  height: 90vw;
  max-width: 380px;
  max-height: 380px;
  overflow: hidden;
}
.col-item {
  width: 100%;
  height: 100%;
  display: inline-block;
}
.red {
  background-color: #FFC1C1;
}
.yellow {
  background-color: #EEEE00;
}
.blue {
  background-color: #1E90FF;
}
.green {
  background-color: #66CD00;
}
.color-btn-wrap {
  padding: 20px 0;
  display: flex;
  align-items: center;
  justify-content: space-around;
}
.color-btn {
  border-radius: 10px;
  width: 10vw;
  height: 20vw;
  max-width: 80px;
  max-height: 120px;
}
.rule-wrap {
  padding: 15px;
  color: #ccc;
  font-size: 15px;
}
.over-panel {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  text-align: center;
  background-color: rgba(0, 0, 0, 0.2);
}
.over-text {
  font-size: 18px;
  color: #4B0082;
  margin-top: 150px;
  font-weight: bold;
  animation: trans-animation 2s linear .1s infinite alternate;
}
.restart {
  margin-top: 30px;
  padding: 10px 15px;
  background-color: #B0E2FF;
  border-radius: 15px;
  display: inline-block;
  color: #fff;
}
.success-panel {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  text-align: center;
  background-color: rgba(0, 0, 0, 0.2);
}
.success-text {
  font-size: 18px;
  color: #333;
  margin-top: 150px;
  font-weight: bold;
  animation: trans-animation 2s linear .1s infinite alternate;
}
.next-pass {
  margin-top: 30px;
  padding: 10px 15px;
  background-color: #BCEE68;
  border-radius: 15px;
  display: inline-block;
  color: #fff;
}
@keyframes trans-animation {
  from {transform: scale(0.9)}
  to {transform: scale(1.3)}
}
</style>

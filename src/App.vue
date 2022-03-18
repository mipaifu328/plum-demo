<!--
 * @Descripttion: vue
 * @version: 1.0
 * @Author: mipaifu328
 * @Date: 2022-03-17 19:57:58
 * @LastEditors: mipaifu328
 * @LastEditTime: 2022-03-18 15:57:06
-->
<script setup lang="ts">
const el = $ref<HTMLCanvasElement>()
const ctx = $computed(() => el!.getContext('2d')!) // ! 断言这个值必然存在
const WIDTH = 600
const HEIGHT = 600

interface Point {
  x: number
  y: number
}

interface Branch {
  start: Point
  length: number
  theta: number
}

onMounted(() => {
  init()
})

function init() {
  ctx.strokeStyle = 'rgba(0,0,0,0.4)'
  step({
    start: { x: WIDTH / 2, y: HEIGHT },
    length: 20,
    theta: -Math.PI / 2,
  })
}

const pendingTasks: Function[] = []

function step(b: Branch, depth = 0) {
  const end = getEndPoint(b)
  drawBranch(b)
  if (depth < 2 || Math.random() < 0.5) {
    pendingTasks.push(() => {
      step({
        start: end,
        length: 20 + (Math.random() * 10 - 5),
        theta: b.theta - 0.3 * Math.random(),
      }, depth + 1)
    })
  }
  if (depth < 2 || Math.random() < 0.5) {
    pendingTasks.push(() => {
      step({
        start: end,
        length: 20 + (Math.random() * 10 - 5),
        theta: b.theta + 0.3 * Math.random(),
      }, depth + 1)
    })
  }
}

function frame() {
  const tasks = [...pendingTasks]
  pendingTasks.length = 0
  tasks.forEach(fn => fn())
}

let frameCount = 0
function startFrame() {
  frameCount++
  requestAnimationFrame(() => {
    if (frameCount % 3 === 0)
      frame()
    startFrame()
  })
}
startFrame()
function getEndPoint(b: Branch): Point {
  return {
    x: b.start.x + b.length * Math.cos(b.theta),
    y: b.start.y + b.length * Math.sin(b.theta),
  }
}

function drawBranch(b: Branch) {
  lineTo(b.start, getEndPoint(b))
}

function lineTo(p1: Point, p2: Point) {
  ctx.beginPath()
  ctx.moveTo(p1.x, p1.y)
  ctx.lineTo(p2.x, p2.y)
  ctx.stroke()
}
</script>

<template>
  <canvas ref="el" width="600" height="600" border />
</template>

<style scoped></style>

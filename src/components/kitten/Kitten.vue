<script setup lang="ts">
import { reactive, ref, onMounted, onBeforeUnmount, watch } from "vue"

// Components
import {
  KittenBody,
  KittenHead,
  KittenLeg,
  KittenTail
} from "@/components/kitten"

// Component Refs
const wrapper = ref<HTMLInputElement | null>(null)
const cat = ref<HTMLInputElement | null>(null)
const catHead = ref<HTMLInputElement | null>(null)

// Component State
const pos = reactive<Position>({
  x: null,
  y: null
})
const legClasses = reactive<LegClasses>({
  leg: true,
  walk: false
})
const catClasses = reactive<CatClasses>({
  cat: true,
  first_pose: true
})
const catStyles = reactive<CatStyles>({})
const headStyles = reactive<HeadStyles>({})
// Component Methods
const walk = () => {
  catClasses.first_pose = false
  legClasses.walk = true
}

const handleMouseMotion = (event: MouseEventInit) => {
  pos.x = event.clientX
  pos.y = event.clientY
  walk()
}

//const handleTouchMotion = (e: TouchEventInit) => {
//  if (!e.targetTouches) return
//  // @ts-ignore: Unreachable code error
//  const touch: MouseEvent = e.targetTouches[0]
//  pos.x = touch.offsetX
//  pos.y = touch.offsetY
//  walk()
//}

const turnRight = () => {
  catStyles.left = `${Number(pos.x) - 90}px`
  catClasses.face_left = false
  catClasses.face_right = true
}

const turnLeft = () => {
  catStyles.left = `${Number(pos.x) + 10}px`
  catClasses.face_left = true
  catClasses.face_right = false
}

const decideTurnDirection = () => {
  Number(cat.value?.getBoundingClientRect().x) < Number(pos.x)
    ? turnRight()
    : turnLeft()
}

const headMotion = () => {
  Number(pos.y) > Number(wrapper.value?.clientHeight) - 100
    ? (headStyles.top = "-15px")
    : (headStyles.top = "-30px")
}

// ! BUGGING OUT HERE POSITION X !== CAT.OFFSETLEFT
const decideStop = () => {
  console.log(pos.x, " <<< x")
  console.log(cat.value?.offsetLeft, " <<< offsetleft")

  if (
    (catClasses.face_right &&
      Number(pos.x) - 90 === Number(cat.value?.offsetLeft)) ||
    (catClasses.face_left &&
      Number(pos.x) + 10 === Number(cat.value?.offsetLeft))
  ) {
    legClasses.walk = false
  }
}

// Component Lifecycle Hooks
onMounted(() => {
  document.addEventListener("mousemove", handleMouseMotion)
  //document.addEventListener("mousemove", handleTouchMotion)
})

watch(
  () => pos,
  (newPos) => {
    if (!newPos.x || !newPos.y) return
    decideTurnDirection()
    headMotion()
    decideStop()
  },
  { deep: true }
)

onBeforeUnmount(() => {
  document.removeEventListener("mousemove", handleMouseMotion)
  //document.removeEventListener("mousemove", handleTouchMotion)
})
</script>

<template>
  <h1>
    {{ pos.x }}
  </h1>
  <div class="outer_wrapper">
    <div ref="wrapper" class="wrapper">
      <div class="cat_wrapper">
        <div ref="cat" :class="catClasses" :style="catStyles">
          <!-- START HEAD -->
          <div ref="catHead" class="cat_head" :style="headStyles">
            <kitten-head />
          </div>
          <!-- END HEAD -->

          <!-- START BODY -->
          <div class="body">
            <kitten-body />
            <div class="tail">
              <kitten-tail />
            </div>
          </div>
          <!-- END BODY -->

          <!-- START LEGS -->
          <div class="front_legs">
            <div class="one" :class="legClasses">
              <kitten-leg />
            </div>
            <div class="two" :class="legClasses">
              <kitten-leg />
            </div>
          </div>
          <div class="back_legs">
            <div class="three" :class="legClasses">
              <kitten-leg />
            </div>
            <div class="four" :class="legClasses">
              <kitten-leg />
            </div>
          </div>
          <!-- END LEGS -->
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
@import "@/assets/styles/kitten.scss";
</style>

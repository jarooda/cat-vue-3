<script setup lang="ts">
import { RouterView } from "vue-router"
import KittenComponent from "@/components/kitten/Kitten.vue"
</script>

<template>
  <kitten-component />
  <RouterView />
</template>

<style lang="scss">
@use "@/assets/styles/color.scss";
@import "@/assets/base.css";

#app {
  font-weight: normal;
  height: 100vh;
  background: color.$black;
  color: white;
}
</style>

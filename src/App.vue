<script setup>
import { RouterView } from 'vue-router'
</script>

<template>
  <RouterView />
</template>

<style>
#app {
  height: 100vh;
  width: 100vw;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
  padding: 0;
  box-sizing: border-box;
  background-color: #FFEFD5;
}

#app * {
  box-sizing: border-box;
}
</style>

<script setup>
import music_page from '../src/components/music_page.vue'
import Footer from './components/FooterComp.vue'
</script>

<template>
  <div>
    <music_page />
    <Footer />
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Cairo:wght@200..1000&display=swap');
html {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: 'Cairo', sans-serif;
  font-size: 12px;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>

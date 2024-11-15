<template>
  <Writer
    :texts="['TEXT1 HAHA', 'TEXT2 CCC']"
    :eraseDelay="100"
    :typeDelay="100"
    :waitTimer="1800"
  >
    <template #first_word="{ text }">
      <span style="color: red;">
        {{ text }}
      </span>
    </template>
    <template #rest_word="{ text }">
      <span style="color: blue; margin-left: 4px;">
        {{ text }}
      </span>
    </template>
  </Writer>
</template>

<script setup lang="ts">
import Writer from './components/Writer.vue';
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

<script setup>
import { ref } from "vue";
const res = ref("res初期値");
const url = ref("url初期値");

function getUrl() {
  res.value = url.value;
}
</script>

<template>
  <input type="text" v-bind:value="url" />
  <button @click="getUrl">Click</button>
  <p>{{ res }}</p>
</template>

<style scoped></style>

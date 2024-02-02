<script setup lang="ts">
import MonacoEditor from './components/MonacoEditor.vue';
</script>

<template>
  <MonacoEditor
    value="console.log('Hello, vuemonaco !')"
    language="typescript"
    style="height: 30vh"
  />
</template>

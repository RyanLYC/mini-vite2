<template>
  <div>{{ title }}</div>
</template>

<script>
import { ref } from "vue";
export default {
  setup() {
    const title = ref("hello, min vite !");
    return { title };
  },
};
</script>

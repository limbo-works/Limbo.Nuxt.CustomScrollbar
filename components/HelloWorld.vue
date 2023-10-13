<script setup>
const { myLayer } = useAppConfig();
</script>

<template>
  <div class="hellothere">
    <h1>Hello World!</h1>
    <pre>{{ myLayer }}</pre>
  </div>
</template>
<style>
.hellothere {
  height: 200vh;
}
</style>

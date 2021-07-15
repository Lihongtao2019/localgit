<template>
  <input type="number" placeholder="0" :id="name" :value="info" />
</template>

<script>
export default {
  props: {
    name: String,
    info: String,
  },
};
</script>
<style scoped>
</style>
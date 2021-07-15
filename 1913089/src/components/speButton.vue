<template>
  <button @click="$emit('option')">{{ name }}</button>
</template>

<script>
export default {
  props: {
    name: String,
  },
};
</script>

<style scoped>
</style>
<template>
  <div>
    <span class="counter">{{ counter }}</span>
    <button @click="incrementCounter">+</button>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      counter: 0,
    };
  },
  methods: {
    incrementCounter() {
      this.counter++;
    }
  }
}
</script>

<style scoped>
  .counter {
    color: #f00;
  }
</style>

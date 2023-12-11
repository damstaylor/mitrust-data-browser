<template>
  <div id="app">
    <code v-if="json">{{ json }}</code>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      json: null,
    }
  },
  mounted() {
    this.loadJSON()
  },
  methods: {
    async loadJSON() {
      try {
        const response = await fetch('https://app.m-itrust.com/v2/public/claims')
        this.json = await response.json()
      } catch (error) {
        console.error('Failed fetching JSON grammar file', error.message)
      }
    }
  },
}
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

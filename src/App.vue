<template>
  <div id="app">
    <b-container>
      <span v-if="!isLoaded">Loading...</span>
      <b-table v-else :items="scopesArray" :fields="fields">

      </b-table>
    </b-container>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isLoaded: false,
      json: null,
      fields: ['_key', 'claim_type', 'desc_2', 'examples'],
    }
  },
  mounted() {
    this.loadJSON()
  },
  computed: {
    jsonData() {
      return this.json?.data || null
    },
    scopes() {
      return this.jsonData?.scopes || null
    },
    scopesArray() {
      return this.scopes ? Object.entries(this.scopes).map(item => ({
          _key: item[0],
          ...item[1]
        })
      ) : []
    },
    deprecated() {
      return this.jsonData?.deprecated || null
    },
    markers() {
      return this.jsonData?.markers || null
    },
  },
  methods: {
    async loadJSON() {
      try {
        const response = await fetch('https://app.m-itrust.com/v2/public/claims')
        this.json = await response.json()
        this.isLoaded = true
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

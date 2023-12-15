<template>
  <div id="app">
    <b-container fluid>
      <span v-if="!isLoaded">Loading...</span>
      <collapsible-table v-else
                         dark
                         :items="formattedScopesArray"
                         :fields="mainFields"
                         :format-object-as-array="formatObjectAsArray"
      />
    </b-container>
  </div>
</template>

<script>
import CollapsibleTable from '@/components/collapsible-table.vue'

export default {
  name: 'App',
  components: {CollapsibleTable},
  data() {
    return {
      isLoaded: false,
      json: null,
      mainFields: ['_key', 'claim_type', 'desc_2', 'examples', 'openid', 'show_details'],
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
    formattedScopesArray() {
      return this.scopes ? Object.entries(this.scopes).map(item => ({
            _key: item[0],
            ...item[1],
            _showDetails: false
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
    // { key1: value1, key2: value2, ... } => [ { _key: key1, value1, _showDetails: false }, ... ]
    formatObjectAsArray(obj) {
      let res = Object.entries(obj).map(item => ({
            _key: item[0],
            ...item[1],
            _showDetails: false
          })
      )
      console.log(res)
      return res
    },
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

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

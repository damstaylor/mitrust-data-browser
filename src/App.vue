<template>
  <div id="app">
    <span v-if="!isLoaded">Loading...</span>
    <b-container v-else fluid>
      <h1>Scopes</h1>
      <collapsible-table :items="formattedScopesArray"
                         :fields="mainFields"
                         :format-object-as-array="formatObjectAsArray"
      />
      <div v-if="markers" class="markers">
        <h1>Markers</h1>
        <b-table :items="markers" :fields="['marker', 'description']"/>
      </div>
      <div v-if="deprecated" class="deprecated">
        <h1>Deprecated</h1>
        <collapsible-table :items="formatObjectAsArray(deprecated)"
                           :fields="deprecatedFields"
                           :format-object-as-array="formatObjectAsArray"
        />
      </div>
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
    deprecatedFields() {
      let fields = this.mainFields.slice();
      fields.splice(-1, 0, 'replaced_by')
      return fields
    },
    jsonData() {
      return this.json?.data || null
    },
    scopes() {
      return this.jsonData?.scopes || null
    },
    formattedScopesArray() {
      return this.scopes ? this.formatObjectAsArray(this.scopes) : []
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
      return Object.entries(obj).map(item => ({
            _key: item[0],
            ...item[1],
            _showDetails: false
          })
      )
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

table, .collapsible-table {
  margin-bottom: 80px;
}
</style>

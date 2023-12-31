<template>
  <div id="app">
    <div v-if="!isLoaded" class="loading">
      <div>Loading...</div>
      <b-spinner />
    </div>
    <b-container v-else fluid>
      <h1>Scopes</h1>
      <collapsible-table :items="formattedScopesArray"
                         :fields="mainFields"
                         :format-object-as-array="formatObjectAsArray"
                         :fixed="true"
      />
      <div v-if="markers" class="markers">
        <h1>Markers</h1>
        <collapsible-table :items="markers"
                           :fields="['marker', 'description']"
                           outlined
                           borderless
        />
      </div>
      <div v-if="deprecated" class="deprecated">
        <h1>Deprecated</h1>
        <collapsible-table :items="formatObjectAsArray(deprecated)"
                           :fields="deprecatedFields"
                           :format-object-as-array="formatObjectAsArray"
                           :fixed="true"
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
      mainFields: ['_key', 'claim_type', 'desc_2', 'examples', 'openid', 'as_list'],
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
    formatObjectAsArray(obj, showDetails = false) {
      return Object.entries(obj).map(item => ({
            _key: item[0],
            ...item[1],
            _showDetails: showDetails
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
  margin: 60px 80px;
  height: 100vh;
}

table {
  margin-bottom: 80px;
}

/deep/ table thead {
  border-bottom: 1px solid var(--bs-border-color);
}

.loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
}

.loading .spinner-border {
  margin-top: 20px;
}
</style>

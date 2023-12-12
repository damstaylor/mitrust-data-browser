<template>
  <div class="collapsible-table">
    <b-table :dark="dark"
             :items="items"
             :fields="fields"
             :thead-class="noHeader ? 'd-none' : ''"
             fixed
    >
      <template #cell(show_details)="row">
        <b-button v-if="row.item.claim_type === 'recursive'"
                  size="sm" @click="log(row)"
                  class="mr-2"
        >
          {{ row.detailsShowing ? 'Hide' : 'Show' }} Details
        </b-button>
      </template>
      <template #cell(examples)="row">
        <ul>
          <li v-for="(item, key) of row.item.examples" :key="key">{{ item }}</li>
        </ul>
      </template>
      <template #cell(openid)="row">
        <span>{{ !!row.item.openid ? '✅' : '❌' }}</span>
      </template>
      <template #row-details="row">
        <collapsible-table v-if="row?.item?.claim_type === 'recursive'"
                           no-header
                           borderless
                           :dark="!dark"
                           :items="getOtherRowItems(row.item)"
                           :fields="fields"
        />
        <b-card v-else>
          <code>{{ row.item }}</code>
          <ul>
            <li v-for="(value, key) in row.item" :key="key">{{ key }}: {{ value }}</li>
          </ul>
        </b-card>
      </template>
    </b-table>
  </div>
</template>


<script>
export default {
  name: 'CollapsibleTable',
  props: {
    items: {type: Array, default: () => []},
    fields: {type: Array, default: () => []},
    dark: {type: Boolean, default: false},
    noHeader: {type: Boolean, default: false},
  },
  methods: {
    log(row) {
      row.toggleDetails()
      console.log(row)
    },
    formatScopesObjAsArray(scopes) {
      return scopes ? Object.entries(scopes).map(item => ({
            _key: item[0],
            ...item[1],
            _showDetails: false
          })
      ) : []
    },
    getOtherRowItems(row) {
      let sanitizedRow = {...row}
      delete sanitizedRow.desc_2
      delete sanitizedRow.claim_type
      delete sanitizedRow.openid
      delete sanitizedRow._key
      delete sanitizedRow._showDetails
      return this.formatScopesObjAsArray(sanitizedRow)
    },
  },
}
</script>

<style scoped>
menu:not(article menu), ol:not(article ol), ul:not(article ul) {
  list-style: none;
}
</style>

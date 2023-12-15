<template>
  <div class="collapsible-table">
    <b-table :dark="dark"
             :items="items"
             :fields="fields"
             :thead-class="noHeader ? 'd-none' : ''"
             fixed
    >
      <template #cell(show_details)="row">
        <b-button v-if="row.item.claim_type && Object.keys(sanitizeItem(row.item)).length"
                  size="sm"
                  @click="row.toggleDetails"
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
        <collapsible-table v-if="row.item.claim_type && Object.keys(sanitizeItem(row.item)).length"
                           no-header
                           borderless
                           :dark="!dark"
                           :items="formatObjectAsArray(sanitizeItem(row.item))"
                           :fields="fields"
        />
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
    sanitizeItem(item) {
      let sanitizedItem = {...item}
      delete sanitizedItem.claim_type
      delete sanitizedItem.desc_2
      delete sanitizedItem.examples
      delete sanitizedItem.openid
      delete sanitizedItem._key
      delete sanitizedItem._showDetails
      return sanitizedItem
    },
  },
}
</script>

<style scoped>
menu:not(article menu), ol:not(article ol), ul:not(article ul) {
  list-style: none;
}
</style>

<template>
  <div class="collapsible-table">
    <b-table :dark="dark"
             :items="items"
             :fields="fields"
             :thead-class="noHeader ? 'd-none' : ''"
             fixed
    >
      <template #cell(show_details)="row">
        <b-button v-if="hasRowItemKeys(row)"
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
        <collapsible-table v-if="hasRowItemKeys(row)"
                           no-header
                           borderless
                           :dark="!dark"
                           :items="getSubNodesFromRow(row)"
                           :fields="fields"
                           :format-object-as-array="formatObjectAsArray"
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
    formatObjectAsArray: {type: Function, default: () => {}},
  },
  computed: {
    getSanitizedItemFromRow() {
      return row => this.sanitizeItem(row.item)
    },
    getSubNodesFromRow() {
      return row => this.formatObjectAsArray(this.getSanitizedItemFromRow(row))
    },
    hasRowItemKeys() {
      return row => Object.keys(this.getSanitizedItemFromRow(row)).length > 0
    },
  },
  methods: {
    sanitizeItem(item) {
      const {claim_type, desc_2, examples, openid, _key, _showDetails, replaced_by, ...rest} = item // eslint-disable-line
      return rest
    },
  },
}
</script>

<style scoped>
menu:not(article menu), ol:not(article ol), ul:not(article ul) {
  list-style: none;
}
</style>

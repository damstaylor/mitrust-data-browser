<template>
  <div class="collapsible-table">
    <b-table borderless
             :items="items"
             :fields="fields"
             :thead-class="noHeader ? 'd-none' : ''"
             :fixed="fixed"
    >
      <template #cell(_key)="row">
        <button v-if="hasRowItemKeys(row)"
                @click="row.toggleDetails"
        >
          {{ row.detailsShowing ? '▼' : '▶' }}
        </button>
        {{ row.item._key }}
      </template>
      <template #cell(examples)="row">
        <ul>
          <li v-for="(item, key) of row.item.examples" :key="key">{{ item }}</li>
        </ul>
      </template>
      <template #cell(openid)="row">
        <span>{{ !!row.item.openid ? '✓' : '⨯' }}</span>
      </template>
      <template #row-details="row">
        <collapsible-table v-if="hasRowItemKeys(row)"
                           no-header
                           borderless
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
    fixed: {type: Boolean, default: false},
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
      let sanitizedItem = {...item}
      this.fields.forEach(field => {
        delete sanitizedItem[field]
      });
      delete sanitizedItem._showDetails
      delete sanitizedItem._key
      return sanitizedItem
    },
  },
}
</script>

<style scoped>
.collapsible-table {
  margin-bottom: 80px;
  border: 1px solid var(--bs-border-color);
}

/deep/ thead {
  border-bottom: 1px solid var(--bs-border-color);
}

/deep/ td {
  position: relative;
}

menu:not(article menu), ol:not(article ol), ul:not(article ul) {
  list-style: none;
}


button {
  border: none;
  background-color: unset;
  width: 24px;
  height: 24px;
  position: absolute;
  left: 32px;
}
</style>

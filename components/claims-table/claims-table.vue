<template>
  <div>
    <v-layout row wrap align-center justify-space-between>
      <h3 class="grey--text mr-5" v-text="title" />
      <v-card flat tile height="50" width="200" class="mb-3">
        <v-text-field
          v-model="search"
          append-icon="search"
          label="Filter"
          hide-details
          single-line
        />
      </v-card>
    </v-layout>
    <v-data-table
      class="claims-table"
      sort-icon="keyboard_arrow_up"
      :rows-per-page-items="rowOptions"
      :hide-actions="hideActions"
      :headers="headers"
      :search="search"
      :items="claims"
      disable-initial-sort
    >
      <template slot="items" slot-scope="props">
        <tr>
          <td><div class="label" v-text="props.item.type" /></td>
          <td>
            <component
              class="label"
              :is="getTag(props.item.issuer)"
              :to="getLink(props.item.issuer, 'address')"
              v-text="props.item.issuer"
            />
          </td>
          <td>
            <component
              class="label"
              :is="getTag(props.item.subject)"
              :to="getLink(props.item.subject, 'address')"
              v-text="props.item.subject"
            />
          </td>
          <td><div class="label" v-text="props.item.hash" /></td>
          <td><div class="label" v-text="props.item.date" /></td>
          <td>
            <component
              class="label"
              :is="getTag(props.item.txHash)"
              :to="getLink(props.item.txHash, 'tx')"
              v-text="props.item.txHash"
            />
          </td>
        </tr>
      </template>
    </v-data-table>
  </div>
</template>

<script>
export default {
  name: "v-claims-table",
  props: {
    title: {
      type: String,
      required: true
    },
    claims: {
      type: Array,
      required: true
    },
    selected: {
      type: String
    }
  },
  data() {
    return {
      search: "",
      headers: [
        {
          text: "Type",
          value: "type",
          width: "12%"
        },
        {
          text: "Issuer",
          value: "issuer",
          width: "20%"
        },
        {
          text: "Subject",
          value: "subject",
          width: "20%"
        },
        {
          text: "Digital Fingerprint",
          value: "hash",
          width: "20%"
        },
        {
          text: "Age",
          value: "date",
          width: "8%"
        },
        {
          text: "Transaction",
          value: "txHash",
          width: "20%"
        }
      ],
      rowOptions: [25, 100]
    }
  },
  computed: {
    hideActions() {
      return this.claims.length < 30
    }
  },
  methods: {
    getTag(v) {
      return v === this.selected ? "div" : "nuxt-link"
    },
    getLink(v, type) {
      switch (type) {
        case "tx":
          return `/explore/tx/${v}`
        default:
          return `/explore/${v}`
      }
    }
  }
}
</script>

<style lang="stylus">
.claims-table td {
  max-width: 0 !important;
  height: 48px !important;

  .label {
    margin-right: 12px;
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    display: block;
  }
}
</style>

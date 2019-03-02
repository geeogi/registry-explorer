<template>
  <v-claims-table :title="title" :claims="claims" />
</template>

<script>
import {
  zincRegistry,
  eventsToClaims,
  etherscanQueryUrl
} from "../../core/explorer-utils"

export default {
  async asyncData({ $etherscan, store }) {
    const network = store.state.config.ethereumNetwork
    const url = etherscanQueryUrl(network)
    const { data } = await $etherscan.get(url)
    return { events: data.result, network }
  },
  computed: {
    title() {
      return `Registry: ${zincRegistry(this.network)}`
    },
    claims() {
      return eventsToClaims(this.events).reverse()
    }
  }
}
</script>

<template>
  <v-claims-table :title="title" :claims="claims" :selected="address" />
</template>

<script>
import { uniq } from "ramda"
import { eventsToClaims, etherscanQueryUrl } from "../../core/explorer-utils"

export default {
  async asyncData({ $etherscan, params, store }) {
    const network = store.state.config.ethereumNetwork
    const address = params.address
    const issuerUrl = etherscanQueryUrl(network, "issuer", address)
    const subjectUrl = etherscanQueryUrl(network, "subject", address)
    const issuerRes = await $etherscan.get(issuerUrl)
    const subjectRes = await $etherscan.get(subjectUrl)
    const issuerEvents = issuerRes.data.result
    const subjectEvents = subjectRes.data.result
    const events = uniq([...subjectEvents, ...issuerEvents])
    return { events, network }
  },
  computed: {
    address() {
      return this.$route.params.address
    },
    title() {
      return `Address: ${this.address}`
    },
    claims() {
      return eventsToClaims(this.events).reverse()
    }
  }
}
</script>

<template>
  <v-claims-table :title="title" :claims="[claim]" :selected="txHash" />
</template>

<script>
import { propEq } from "ramda"
import {
  etherscanQueryUrl,
  extractPropFromEvent
} from "../../../core/explorer-utils"

export default {
  async asyncData({ $etherscan, error, params, store }) {
    const network = store.state.config.ethereumNetwork
    const url = etherscanQueryUrl(network)
    const isHash = propEq("transactionHash", params.tx)
    const { data } = await $etherscan.get(url)
    const match = data.result.find(isHash)
    const event = match || error({ statusCode: 404, message: "Tx not found" })
    return { event }
  },
  computed: {
    txHash() {
      return this.$route.params.tx
    },
    title() {
      return `Transaction Id: ${this.txHash}`
    },
    claim() {
      return {
        type: extractPropFromEvent("type", this.event),
        hash: extractPropFromEvent("hash", this.event),
        date: extractPropFromEvent("date", this.event),
        txHash: extractPropFromEvent("txHash", this.event),
        issuer: extractPropFromEvent("issuer", this.event),
        subject: extractPropFromEvent("subject", this.event)
      }
    }
  }
}
</script>

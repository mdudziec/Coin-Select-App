<template>
  <tr @click="goToCoinDetails(coins.id)">
    <td>{{ index + 1 }}</td>
    <td>
      <img :src="coins.image" />{{ coins.name }}
      <span>{{ symbolToUpperCase(coins.symbol) }}</span>
    </td>
    <td>{{ coins.current_price }} &#8364;</td>
    <td
      v-bind:class="[
        coins.price_change_percentage_24h > 0 ? 'positive' : 'negative',
      ]"
    >
      {{ coins.price_change_percentage_24h }} &#37;
    </td>
    <td>{{ tousandsCoverter(coins.total_volume) }} &#8364;</td>
  </tr>
</template>

<script>
export default {
  name: "CoinRow",
  data() {
    return {};
  },
  props: {
    coins: {
      type: Object,
      required: true,
    },
    index: {
      type: Number,
      required: true,
    },
  },
  methods: {
    tousandsCoverter(volume) {
      return new Intl.NumberFormat().format(volume);
    },
    symbolToUpperCase(symbol) {
      return symbol.toUpperCase();
    },
    goToCoinDetails() {
      this.$router.push({
        name: "CoinDetails",
        params: { coinId: this.coins.id },
      });
    },
  },
};
</script>

<style lang="sass" scoped>

// Color pallet
$grey: #e5e5e5
$green: #006A4E
$red: #EF0107


img
    width:20px
    margin: 0.2rem 0.1rem

span
    opacity:0.5
    margin-left:1rem

.negative
    color: $red

.positive
    color: $green

@media (max-width: 28.75em)
  img
    width:10px
    margin: 0.2rem 0.1rem
</style>

<template>
  <div class="table__container">
    <table>
      <thead>
        <tr>
          <th v-for="(column, index) in columns" :key="index">
            {{ column.field }}
          </th>
        </tr>
      </thead>
      <tbody>
        <CoinRow
          v-for="(coins, index) in filteredCoinsList"
          :key="index"
          :coins="coins"
          :index="index"
        />
      </tbody>
    </table>
  </div>
  <div class="table__pagination">
    <router-link
      id="previous-page"
      :to="{ name: 'HomePage', query: { page: page - 1 } }"
      rel="prev"
      v-if="page != 1"
      >&#60;&#60; Previous</router-link
    >
    <router-link
      id="next-page"
      :to="{ name: 'HomePage', query: { page: page + 1 } }"
      rel="next"
      >Next &#62;&#62;</router-link
    >
  </div>
</template>

<script>
import APIService from "../services/APIservice";
import CoinRow from "@/components/CoinRow.vue";

const ITEMS_PER_PAGE = 10;

export default {
  name: "CoinTable",
  components: {
    CoinRow,
  },
  data() {
    return {
      columns: [
        {
          label: "#",
          field: "#",
        },
        {
          label: "coin-name",
          field: "Coin",
        },
        {
          label: "coin-price",
          field: "Price",
        },
        {
          label: "coin_price_change",
          field: "Price change",
        },
        {
          label: "24_volume",
          field: "24 Volume",
        },
      ],
      coinsList: null,
      filteredCoinsList: [],
      loading: true,
      errored: false,
    };
  },
  props: {
    searchbarInput: {
      type: String,
      required: true,
    },
    page: {
      type: Number,
      required: true,
    },
  },
  watch: {
    searchbarInput() {
      this.checkSearchInput();
    },
    page() {
      this.getCoinsFromServer();
      this.checkSearchInput();
    },
  },
  methods: {
    checkSearchInput() {
      if (this.searchbarInput.length > 0) {
        this.filteredCoinsList = this.coinsList.filter((coin) => {
          const searchPhrase = this.searchbarInput.toLowerCase();
          const { name, symbol } = coin;
          if (
            name.toLowerCase().indexOf(searchPhrase) >= 0 ||
            symbol.toLowerCase().indexOf(searchPhrase) >= 0
          ) {
            return true;
          }
        });
      } else {
        this.filteredCoinsList = this.coinsList;
      }
    },
    getCoinsFromServer() {
      APIService.getCoins(ITEMS_PER_PAGE, this.page)
        .then((response) => {
          this.coinsList = response.data;
          this.checkSearchInput();
        })
        .catch((error) => {
          console.log(error);
          this.errored = true;
        })
        .finally(() => (this.loading = false));
    },
  },
  mounted() {
    this.getCoinsFromServer();
  },
};
</script>
<style lang="sass">

// color pallet
$grey: #e5e5e5
$white: #F0F0F0
$black: #000000


.table__container
  position: relative
  width:100%
  display: flex
  flex-direction: row

table
  width:100%
  max-width: 100%
  margin-bottom:20px
  background-color: $grey
  border-spacing: 0
  overflow:hidden
  border-collapse: collapse
  display: table
  border-spacing: 2px
  margin: 0 1rem 0 1rem

thead
  background-color: $grey
  color: $black
  height: 2rem

tbody
  display: table-row-group
  vertical-align: middle
  border-color: inherit

tr
  display: table-row
  vertical-align: inherit
  border-color: inherit
  height: 3rem

tr:hover
  background-color: $white

th
  text-align: left
  padding:0.5rem
  border-bottom: 1px solid $black

th:nth-child(1)
  border-radius: 0.5rem 0 0 0.5rem

th:nth-child(5)
  border-radius:  0 0.5rem 0.5rem 0

td
  display: table-cell
  vertical-align: inherit
  padding:0.5rem
  border-bottom: 1px solid rgba(0,0,0,.12)

td:nth-child(1)
  opacity:0.5

@media (max-width: 28.75em)
  td
    padding: 0rem

.table__pagination
  display: flex
  justify-content: center
  align-items: center
  align-content: center
  flex-wrap: nowrap
  text-decoration: none
  color: black
  padding-top: 1rem

  a
    flex: 1
    text-decoration: none
    color: black

#previous-page
  text-align: left

#next-page
  text-align: right
</style>

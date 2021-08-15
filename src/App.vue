<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="glossy">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          @click="leftDrawerOpen = !leftDrawerOpen"
          aria-label="Menu"
          icon="menu"
        />
        <q-toolbar-title> Coin Gecko </q-toolbar-title>
        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered class="bg-grey-2">
      <q-list>
        <q-item-label header>Essential Links</q-item-label>
        <q-item clickable tag="a" href="/">
          <q-item-section avatar>
            <q-icon name="list" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Coins</q-item-label>
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container class="container">
      <q-input
        outlined
        dense
        standout
        v-model="textSearch"
        placeholder="search"
        class="q-ml-md"
        @keyup="searchCoin"
      >
        <template v-slot:append>
          <q-icon v-if="text === ''" name="search" />
          <q-icon
            v-else
            name="clear"
            class="cursor-pointer"
            @click="text = ''"
          />
        </template>
      </q-input>

      <div class="q-pa-md">
        <q-markup-table>
          <thead>
            <tr class="text-left">
              <th v-for="column in columns" :key="column">
                <h6 class="text-subtitle2 text-weight-bolder">
                  {{ column.label }}
                </h6>
              </th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(coin, index) in filterCoins"
              :key="coin.id"
              class="text-left"
            >
              <td>{{ index + 1 }}</td>
              <td>
                <q-avatar
                  size="40px"
                  font-size="22px"
                  style="margin-right: 10px"
                >
                  <img :src="coin.image" />
                </q-avatar>
                <span style="margin-right: 10px">
                  {{ coin.name }}
                </span>

                <span
                  class="text-uppercase text-caption"
                  text-uppercase
                  text-weight-bolder
                >
                  {{ coin.symbol }}
                </span>
              </td>

              <td>$ {{ coin.current_price }}</td>
              <td
                :class="[
                  coin.price_change_percentage_24h > 0
                    ? 'text-success'
                    : 'text-danger',
                ]"
              >
                {{ coin.price_change_percentage_24h }} %
              </td>

              <td>$ {{ coin.total_volume.toLocaleString() }}</td>
            </tr>
          </tbody>
        </q-markup-table>
      </div>
    </q-page-container>
  </q-layout>
</template>

<script>
import { ref } from "vue";

const columns = [
  {
    name: "no",
    label: "#",
    align: "center",
  },
  {
    name: "name",
    label: "Coin",
    align: "left",
  },
  {
    name: "price",
    label: "$ Price",
    align: "left",
  },
  {
    name: "price_change",
    label: "Price Change (+/-)",
    align: "left",
  },
  {
    name: "24_volume",
    label: "24h Volume",
    align: "left",
  },
];

export default {
  name: "LayoutDefault",

  data() {
    return {
      coins: [],
      filterCoins: [],
      textSearch: "",
    };
  },

  components: {},

  methods: {
    searchCoin() {
      this.filterCoins = this.coins.filter(
        (coin) =>
          coin.name.toLowerCase().includes(this.textSearch.toLowerCase()) ||
          coin.symbol.toLowerCase().includes(this.textSearch.toLowerCase())
      );
    },
  },

  async mounted() {
    const result = await fetch(
      "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false"
    );

    const data = await result.json();
    console.log(data);
    this.coins = data;
    this.filterCoins = data;
  },

  setup() {
    return {
      leftDrawerOpen: ref(false),
      columns,
    };
  },
};
</script>

<style>
.container {
  margin: 25px;
}

.text-success {
  color: green;
}

.text-danger {
  color: red;
}
</style>

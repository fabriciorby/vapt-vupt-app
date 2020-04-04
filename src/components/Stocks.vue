<template>
  <v-container fluid>
    <v-row dense justify="center">
      <v-col cols="12" sm="8" md="6" lg="6">
        <v-card>
          <v-card-title>
            Donchian Semanal 20/20
            <v-spacer></v-spacer>
            <v-text-field
              v-model="search"
              append-icon="mdi-magnify"
              label="Procurar"
              single-line
              hide-details
            ></v-text-field>
          </v-card-title>
          <div class="ml-4">
            <p>Ações com problemas: TIET11, LOGN3, SAPR4.</p>
            <p v-if="!loading">Última atualização em: {{smallCapsDonchianInfo[0].data}}</p>
          </div>
          <v-data-table
            :search="search"
            :loading="loading"
            :loading-text="text"
            :headers="headers"
            :items="smallCapsDonchianInfo"
            :sort-desc="[false, true]"
            item-key="papel"
            sort-by="radar"
            :items-per-page="100"
            multi-sort
            fixed-header
            hide-default-footer
            class="elevation-1"
          >
            <template v-slot:item.papel="{ item }">
              <v-chip v-if="item.inTrade" color="success" dark>{{ item.papel }}</v-chip>
              <v-chip
                v-else-if="!item.inTrade & item.radar < 10"
                color="warning"
                dark
              >{{ item.papel }}</v-chip>
              <v-chip v-else color="error" dark>{{ item.papel }}</v-chip>
            </template>
            <template v-slot:expanded-item="{ headers, item }">
              <td :colspan="headers.length">More info about {{ item.papel }}</td>
            </template>
          </v-data-table>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
const axios = require("axios");
export default {
  name: "Stocks",
  data: () => ({
    search: "",
    text: "Carregando as informações: Donchian Semanal 20/20",
    group: "inTrade",
    loading: true,
    smallCapsTickerList: [],
    smallCapsDonchianInfo: [],
    headers: [
      { text: "Papel", value: "papel" },
      { text: "Preço (R$)", value: "preco" },
      { text: "Start (R$)", value: "start" },
      { text: "Stop (R$)", value: "stop" },
      { text: "Radar (%)", value: "radar" }
      // { text: "Rompeu?", value: "inTrade" },
      // { text: "Data", value: "data" }
    ]
  }),
  mounted() {
    const formatPrice = price => (Number(price) || 0).toFixed(2);
    const formatRadar = (x, y) => (((x - y) / y) * 100).toFixed(2);
    const formatDate = date =>
      new Date(date).toLocaleDateString(undefined, { timeZone: "UTC" });
    axios
      .get("https://b3.fabriciorby.me/smll/all")
      .then(response => (this.smallCapsTickerList = response.data));
    axios
      .get("https://b3.fabriciorby.me/smll/weekly/all/donchian")
      .then(response => {
        this.smallCapsDonchianInfo = response.data.map(i => ({
          papel: i.papel.substring(0, i.papel.length - 3),
          preco: formatPrice(i.preco),
          start: formatPrice(i.start),
          stop: formatPrice(i.stop),
          data: formatDate(i.data),
          radar: i.inTrade
            ? formatRadar(i.preco, i.stop)
            : formatRadar(i.start, i.preco),
          inTrade: i.inTrade
        }));
        this.loading = false;
      });
  }
};
</script>

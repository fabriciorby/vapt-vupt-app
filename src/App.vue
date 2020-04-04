<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-spacer></v-spacer>
            <v-toolbar-title>Radar Vapt-Vupt</v-toolbar-title>
      <v-spacer></v-spacer>
    </v-app-bar>

    <v-content>
      <Stocks />
    </v-content>
  </v-app>
</template>

<script>
import Stocks from "./components/Stocks";

export default {
  name: "App",

  components: {
    Stocks
  },

  data: () => ({
    //
  })
};
</script>
<template>
  <v-app>
    <v-app-bar app color="primary" dark></v-app-bar>

    <v-content>
      <Potsuri />
    </v-content>
  </v-app>
</template>

<script>
import Potsuri from "./components/Potsuri";

export default {
  name: "App",

  components: {
    Potsuri
  },

  data: () => ({})
};
</script>

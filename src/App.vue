<template>
  <v-app>
    <v-main>
      <search :relics="relics" :items="items" />
    </v-main>
  </v-app>
</template>

<script>
import search from "./components/search";
import axios from "axios";
export default {
  name: "App",

  components: {
    search,
  },

  data: () => ({
    relics: [],
    items: [],
  }),
  mounted: async function () {
    const drops = "https://drops.warframestat.us/data/relics.json";
    const response = await axios.get(drops);

    this.relics = response.data.relics;
    this.relics.map(
      (x) => (x.name = x.tier + " " + x.relicName + " " + x.state)
    );
    axios
      .get("https://api.warframe.market/v1/items", {
        headers: { platform: "pc", language: "en" },
      })
      .then((e) => {
        this.items = e.data.payload.items;
      });
  },
};
</script>

<template>
  <div>
    <v-autocomplete
      v-model="model"
      :items="relics"
      item-text="name"
      return-object
      :search-input.sync="search"
      multiple
      chips
      filled
      v-on:input="limiter"
      deletable-chips
      clearable
    ></v-autocomplete>
    <v-row>
      <v-col v-for="(item, i) in model" :key="i">
        <v-card class="ml-2">
          <v-list v-if="model">
            <v-card-title>{{item.name}}</v-card-title>
            <v-card-text>
              <v-list-item v-for="(reward, i) in item.rewards" :key="i">
                <a :href="reward.url" target="_blank">{{reward.itemName}}</a>
                Chance: {{reward.chance}}% Price:{{reward.price}} plat
              </v-list-item>
            </v-card-text>
          </v-list>
        </v-card>
      </v-col>
    </v-row>
    <v-btn @click="rewardPricing" block color="primary">Update</v-btn>
    <!-- <v-btn @click="test" block color="primary">Test</v-btn> -->
  </div>
</template>

<script>
import axios from "axios";
import Vue from "vue";
export default {
  props: ["items", "relics"],
  data() {
    return {
      model: null,
      search: null,
      selectedRelicts: [],
    };
  },
  watch: {
    model: {
      handler: function () {
        this.selectedRelicts = [];
        this.model.forEach((e) => {
          let data = { name: e.itemName, rewards: e.rewards };
          this.selectedRelicts.push(data);
        });
      },
      deep: true,
    },
    selectedRelicts() {},
  },
  methods: {
    limiter(e) {
      if (e.length > 4) {
        alert(" you can only select four", e);
        e.pop();
      }
    },
    async requests(item) {
      const response = await axios.get(
        "https://api.warframe.market/v1/items/" + item.url_name + "/orders",
        {
          headers: { platform: "pc", language: "en" },
        }
      );
      return response.data.payload.orders;
    },
    async rewardPricing() {
      for (let i = 0; i < this.selectedRelicts.length; i++) {
        const relict = this.selectedRelicts[i];

        for (let y = 0; y < relict.rewards.length; y++) {
          const reward = relict.rewards[y];
          let rewardName = reward.itemName;
          if (rewardName == "Forma Blueprint") {
            Vue.set(reward, "price", 0);
          }
          for (let index = 0; index < this.items.length; index++) {
            const element = this.items[index];

            if (rewardName.includes(element.item_name)) {
              //3 per second, 350ms is safe
              await this.sleep(350);
              let test = this.test(element);
              test.then((e) => {
                let url = "https://warframe.market/items/";
                Vue.set(reward, "url", url + element.url_name);
                // Vue.set(reward, "price", e[0].platinum);
                Vue.set(reward, "price", e);
              });
              await this.sleep(350);
            }
          }
        }
      }
      this.selectedRelicts.forEach((rewards) => {
        rewards.rewards.sort((a, b) => b.price - a.price);
      });
    },
    sleep(time) {
      return new Promise((resolve) => setTimeout(resolve, time));
    },
    async test(item) {
      const response = await axios.get(
        "https://api.warframe.market/v1/items/" + item.url_name + "/statistics",
        {
          headers: { platform: "pc", language: "en" },
        }
      );
      let stats = response.data.payload.statistics_closed["48hours"];
      let len = stats.length;
      return stats[len - 1].avg_price;
    },
  },
};
</script>

<style>
</style>
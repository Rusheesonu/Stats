
<template>
  <v-app>
    <v-content>
      <v-app-bar
        app>
        <div class="display-1 ma-auto font-weight-light">Server Statistics</div>
        <v-btn icon large v-on:click="fetchData" style="transition: all 800ms;" :loading="loading">
          <v-icon>mdi-reload</v-icon>
        </v-btn>
      </v-app-bar>
      <div>
        <div class="py-4 my-4 mx-auto d-inline-block" style="width:100%">
          <v-select
            style="max-width: 500px;"
            class="my-4 mx-auto"
            v-model="selectedSource"
            :items="Object.values(dataSources)"
            item-text="source"
            label="Select Source"
            :outlined="true"
          />
          <div v-if="selectedSource != ''" class="ma-4 d-flex justify-center align-center">
            <ServerStatus :loading="loading" :serverDeatils="dataSources[selectedSource]" :size="400" :width="32" textSizeClass="display-2"/>
          </div>
        </div>
        <div v-if="totalLag != -1" class="d-inline-flex flex-column justify-center align-center pa-8" style="position:absolute;top:0;right:0">
          <span class="display-3 font-weight-thin">{{totalLag}}</span>
          <span class="title font-weight-light mt-1">Total Lag</span>
        </div>
      </div>
      <v-divider />
      <div v-if="selectedSource != ''" class="d-flex justify-center flex-row flex-wrap" >
        <span style="flex: 1 1 100%; text-align:center" class="title my-4"> All Sources </span>
          <div class="ma-4" v-for="source in Object.keys(dataSources)" :key="source" >
            <ServerStatus  :serverDeatils="dataSources[source]" :size="150" :width="16" textSizeClass="body-1"/>
          </div>
      </div>
    </v-content>

  <v-footer style="position:fixed; bottom:0; width:100%">
    <v-btn icon large v-on:click="$vuetify.theme.dark = !$vuetify.theme.dark">
      <v-icon>mdi-theme-light-dark</v-icon>
    </v-btn>
    <v-spacer></v-spacer>
    <div>&copy; DataWeave 2020 </div>
  </v-footer>
  </v-app>
</template>

<script>
import ServerStatus from './components/ServerStatus';
import axios from 'axios';

export default {
  name: 'App',

  components: {
    ServerStatus,
  },

  data: () => ({
    dataSources: {},
    selectedSource: "",
    totalLag: -1,
    loading: true
  }),

  mounted () {
      axios
        .get('https://compute.dataweave.com/v6/compute_test/retailer/product_stats/?api_key=6b0f4a4385c17cb13b7718de592ec100')
        .then(response => {
          this.dataSources = response.data.data;
          this.selectedSource = (Object.values(response.data.data)[0]).source;
          this.serverNames = response.data.data.map(e => this.dataSources[e.source] = e);

          this.loading = false;
        });

      axios
        .get('http://api.kafka.dweave.net/kafka/consumer_lag?consumer_group=pr_sync_priceweave_nordstrom_new&topic=pr_sync_priceweave_staging')
        .then(response => {
          this.totalLag = response.data.docs.total_lag;
        });
    },
    methods: {
      fetchData: function () {
        this.loading = true;
        axios
          .get('https://compute.dataweave.com/v6/compute_test/retailer/product_stats/?api_key=6b0f4a4385c17cb13b7718de592ec100')
          .then(response => {
            this.dataSources = response.data.data;
            this.serverNames = response.data.data.map(e => this.dataSources[e.source] = e);
            this.loading = false;
          });

        axios
          .get('http://api.kafka.dweave.net/kafka/consumer_lag?consumer_group=pr_sync_priceweave_nordstrom_new&topic=pr_sync_priceweave_staging')
          .then(response => {
            this.totalLag = response.data.docs.total_lag;
          });
      }
    }
};
</script>

<template >
  <div style="display: flex;
              align-items: center;
              justify-content: center;
              flex-flow: column;">
    <v-progress-circular
        :rotate="-90"
        :size="size"
        :value="serverDeatils.synced_percentage"
        :width="width"
        :indeterminate="loading">
    <span :class="textSizeClass">{{ serverDeatils.synced_percentage + "%"}}</span>
    </v-progress-circular>
    <span class="mt-3" :class="textSizeClass">
      {{serverDeatils.source}}
    </span>
    <span class="blockquote mt-2 pa-0">
      Updated {{date.fromNow()}}
    </span>
    <span class="overline">
      {{date.format('MMMM Do YYYY, h:mm:ss a')}}
    </span>
  </div>
</template>

<script>
import moment from "moment";

export default {
  name: "ServerStatus",

  props: {
    serverDeatils: Object,
    size: Number,
    width: Number,
    textSizeClass: String,
    loading: Boolean
  },

  computed: {
    date: function () {
      return moment(this.serverDeatils.last_updated)
    }
  }
};
</script>

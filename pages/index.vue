<template>
  <v-container>
    <v-row justify="center" align="center">
      <v-col cols="12">
        <v-textarea
          v-model="linksList"
          name="input-7-1"
          label="Paste List Of Links"
          hint="Each Url Should Be On A New Line"
          :disabled="running"
        ></v-textarea>
      </v-col>
    </v-row>
    <v-row
      align="center"
      v-if="!running || totalLinksOpened !== workingArray.length"
    >
      <v-col cols="4">
        <v-btn elevation="2" :loading="running" @click="onRunLinks()"
          >Run Links</v-btn
        >
      </v-col>
      <v-col cols="4">
        <v-text-field
          :disabled="running"
          v-model="delay"
          label="Add A Delay"
          hint="Time in Milliseconds (1000 = 1 Second)"
          type="number"
        ></v-text-field>
      </v-col>
    </v-row>
    <span v-if="running">
      <v-row justify="center" align="center">
        <v-col cols="12">
          <v-progress-linear
            height="10"
            :value="progressValue"
            striped
            color="lime"
          ></v-progress-linear> </v-col
      ></v-row>
      <v-row justify="center" align="center">
        <v-col cols="12">
          <v-card elevation="2" outlined>
            <v-list-item two-line>
              <v-list-item-content>
                <v-list-item-title class="headline">
                  Links Report
                </v-list-item-title>
                <v-card-text>
                  <div>Successful Links: {{ successfulCall }}</div>
                  <div>Failed Links: {{ failedCall }}</div>
                  <div v-if="failedUrls.length > 0">Failed Links</div>
                  <v-list-item v-if="failedUrls.length > 0">
                    <v-list-item-content>
                      <v-list-item-title
                        v-for="failedUrl in failedUrls"
                        :key="failedUrl"
                        >{{ failedUrl }}</v-list-item-title
                      >
                    </v-list-item-content>
                  </v-list-item>
                </v-card-text>
              </v-list-item-content>
            </v-list-item>
          </v-card>
        </v-col></v-row
      >
    </span>
  </v-container>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      counter: 0,
      linksList: "",
      workingArray: [],
      running: false,
      info: null,
      successfulCall: 0,
      failedCall: 0,
      failedLinks: [],
      progressValue: 0,
      mutiplyer: 0,
      totalLinksOpened: 0,
      failedUrls: [],
      delay: 0,
    };
  },
  methods: {
    onRunLinks() {
      this.running = true;
      this.workingArray = this.linksList.split(/\n/);
      let delay = parseInt(this.delay) ? parseInt(this.delay) : 0;
      let timer = delay;

      this.mutiplyer = 100 / this.workingArray.length;

      this.workingArray.forEach((e) => {
        setTimeout(() => {
          this.$axios
            .get(e)
            .then((response) => {
              this.successfulCall = this.successfulCall + 1;
              this.progressValue = this.progressValue + this.mutiplyer;
              this.totalLinksOpened = this.totalLinksOpened + 1;
            })
            .catch((e) => {
              this.failedCall = this.failedCall + 1;
              this.totalLinksOpened = this.totalLinksOpened + 1;
              this.progressValue = this.progressValue + this.mutiplyer;
              this.failedUrls.push(e.config.url);
            });
        }, timer);

        timer = timer + delay;
      });
    },
  },
};
</script>



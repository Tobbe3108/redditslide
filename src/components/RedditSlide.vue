<template>
  <div id="app">
    <input type="text" v-model="subreddit" />
    <select @change="updatePeriod" v-model="sort">
      <option value="hot">Hot</option>
      <option value="new">New</option>
      <option value="controversial">Controversial</option>
      <option value="top">Top</option>
      <option value="rising">Rising</option>
    </select>
    <select v-if="sort === 'top' || sort === 'controversial'" v-model="period">
      <option value="hour">Hour</option>
      <option value="day">Day</option>
      <option value="week">Week</option>
      <option value="month">Month</option>
      <option value="year">Year</option>
      <option value="all">All</option>
    </select>
    <a :href="URL">Go to subreddit</a>

    <div v-if="results">
      <img v-for="post in results" :key="post.name" :src="post.url" :alt="post.name" />
    </div>
    <button @click="$emit('nextPage', results[results.length - 1].name)">Next</button>
  </div>
</template>

<script>
import { isNullOrUndefined } from "util";

export default {
  name: "RedditSlide",
  props: ["subreddit", "sort", "period", "limit", "after", "results"],
  computed: {
    URL: function() {
      let url = "http://localhost:8080/r";

      if (!isNullOrUndefined(this.subreddit)) {
        url += `/${this.subreddit}`;
      }

      if (!isNullOrUndefined(this.sort)) {
        url += `/${this.sort}` + "?";
      }

      if (!isNullOrUndefined(this.period)) {
        url += `&t=${this.period}`;
      }

      return url;
    }
  },
  methods: {
    updatePeriod: function() {
      if (this.sort === "top" || this.sort === "controversial") {
        this.period = "all";
      } else {
        this.period = null;
      }
    }
  }
};
</script>

<style></style>

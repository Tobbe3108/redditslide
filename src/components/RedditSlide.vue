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

    <input type="checkbox" v-model="nsfw" />
    <a :href="URL">Go to subreddit</a>

    <div v-if="results">
      <div v-for="post in results" :key="post.name">
        <div v-if="post.url.includes('gifv')">
          <video
            preload="auto"
            autoplay="autoplay"
            loop="loop"
            style="max-width: 90%; height: auto;"
            crossorigin="anonymous"
          >
            <source
              :src="'https://cors-anywhere.herokuapp.com/' + post.url.slice(0,-4) + 'mp4'"
              type="video/mp4"
            />
          </video>
        </div>
        <div v-else>
          <img
            :src="'https://cors-anywhere.herokuapp.com/' + post.url"
            :alt="post.name"
            crossorigin="anonymous"
            style="max-width: 90%; height: auto;"
          />
        </div>
      </div>
    </div>

    <button @click="$emit('nextPage', results[results.length - 1].name)">Next</button>
  </div>
</template>

<script>
import { isNullOrUndefined } from "util";

export default {
  name: "RedditSlide",
  props: ["subreddit", "sort", "period", "limit", "after", "nsfw", "results"],
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

      url += this.nsfw ? "&nsfw=true" : "&nsfw=false";

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

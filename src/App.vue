<template>
  <div id="app">
    <RedditSlide
      :subreddit="subreddit"
      :sort="sort"
      :period="period"
      :limit="limit"
      :after="after"
      :results="results"
      @nextPage="nextPage"
    />
  </div>
</template>

<script>
import RedditSlide from "./components/RedditSlide.vue";
import axios from "axios";
import { isNullOrUndefined } from "util";

export default {
  name: "app",
  components: {
    RedditSlide
  },
  data() {
    return {
      url: "",
      urlObject: "",
      subreddit: "",
      sort: "",
      period: "",
      limit: "",
      after: "",
      results: []
    };
  },
  created: function() {
    this.url = window.location;
    this.urlObject = new URL(this.url);
    this.subreddit = this.urlObject.pathname.split("/")[2];
    this.sort = this.urlObject.pathname.split("/")[3];
    if (this.sort === "") {
      this.sort = `hot`;
    }
    this.period = this.urlObject.searchParams.get("t");
    this.limit = this.urlObject.searchParams.get("limit");
    this.after = this.urlObject.searchParams.get("after");
    this.fetchPosts();
  },
  computed: {
    generateURL: function() {
      //https://www.reddit.com/r/${subreddit}/${sort}.json?&t=${period}&limit=${limit}&after=${after}

      let url = "https://www.reddit.com/r";

      if (!isNullOrUndefined(this.subreddit)) {
        url += `/${this.subreddit}`;
      }

      if (!isNullOrUndefined(this.sort)) {
        url += `/${this.sort}` + ".json?";
      }

      if (!isNullOrUndefined(this.period)) {
        url += `&t=${this.period}`;
      }

      if (!isNullOrUndefined(this.limit)) {
        url += `&limit=${this.limit}`;
      }

      if (!isNullOrUndefined(this.after)) {
        url += `&after=${this.after}`;
      }

      return url;
    }
  },
  methods: {
    fetchPosts: async function() {
      const res = await axios
        .get("https://cors-anywhere.herokuapp.com/" + this.generateURL)
        .then(res => (res = res.data.data.children));
      let data = res.map(posts => posts.data);
      data = data.filter(
        it => it.domain == "i.imgur.com" || it.domain == "i.redd.it" //||
        //it.domain == "youtu.be" ||
        //it.domain == "imgur.com" ||
        //it.domain == "www.youtube.com"
      );
      this.results = [...this.results, ...data];
    },
    nextPage: function(after) {
      this.after = after;
      this.fetchPosts();
    }
  }
};
</script>

<style></style>

<template>
  <div id="app">
    <RedditSlide
      :subreddit="subreddit"
      :sort="sort"
      :period="period"
      :limit="limit"
      :after="after"
      :results="results"
      @updateSubreddit="updateSubreddit"
    />
  </div>
</template>

<script>
import RedditSlide from "./components/RedditSlide.vue";
import axios from "axios";

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
      results: ""
    };
  },
  created: function() {
    this.url = window.location;
    this.urlObject = new URL(this.url);
    this.subreddit = this.urlObject.pathname.split("/")[2];
    this.sort = this.urlObject.pathname.split("/")[3];
    this.period = this.urlObject.searchParams.get("t");
    this.limit = this.urlObject.searchParams.get("limit");
    this.after = this.urlObject.searchParams.get("after");
    this.fetchPosts(
      this.generateURL(
        this.subreddit,
        this.sort,
        this.period,
        this.limit,
        this.after
      )
    );
  },
  methods: {
    fetchPosts: async function(url) {
      const res = await axios
        .get(url)
        .then(res => (res = res.data.data.children));
      let data = res.map(posts => posts.data);
      data = data.filter(
        it => it.domain == "i.imgur.com" || it.domain == "i.redd.it" //||
        //it.domain == "youtu.be" ||
        //it.domain == "imgur.com" ||
        //it.domain == "www.youtube.com"
      );
      this.results = data;
    },
    generateURL: function(subreddit, sort, period, limit, after) {
      //https://www.reddit.com/r/${subreddit}/${sort}.json?&t=${period}&limit=${limit}&after=${after}

      let url = "https://www.reddit.com/r";

      if (subreddit !== null) {
        url += `/${subreddit}`;
      }

      if (sort === undefined || sort === null) {
        this.sort = "hot";
        url += `/hot` + ".json?";
      } else {
        url += `/${sort}` + ".json?";
      }

      if (period !== null || period !== undefined) {
        url += `&t=${period}`;
      }

      if (limit !== null || limit !== undefined) {
        url += `&limit=${limit}`;
      }

      if (after !== null || after !== undefined) {
        url += `&after=${after}`;
      }

      return url;
    },
    updateSubreddit: function(subreddit, sort, period, limit, after) {
      this.subreddit = subreddit;
      this.sort = sort;
      this.period = period;
      this.limit = limit;
      this.after = after;
    }
  }
};
</script>

<style></style>

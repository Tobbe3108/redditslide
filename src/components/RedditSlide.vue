<template>
  <div id="app">
    <p>Subreddit: {{ subreddit }}</p>
    <p>Sort: {{ sort }}</p>
    <p>Period: {{ period }}</p>

    <input type="text" v-model="subreddit" />
    <input type="text" v-model="sort" />
    <input type="text" v-model="period" />

    <input
      type="submit"
      @click="
        $emit('updateSubreddit', subreddit, sort, period, limit, after, results)
      "
    />

    <div v-if="results">
      <img
        v-for="post in results"
        :key="post.name"
        :src="post.url"
        :alt="post.name"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "RedditSlide",
  props: ["subreddit", "sort", "period", "limit", "after", "results"]
};
</script>

<style></style>

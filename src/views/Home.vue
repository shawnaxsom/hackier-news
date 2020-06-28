<template>
  <div class="home">
    <NewsFeed msg="Welcome to Hackier News" />
  </div>
</template>

<script>
import NewsFeed from "@/components/NewsFeed.vue";

export default {
  name: "Home",
  components: {
    NewsFeed
  }
};
</script>

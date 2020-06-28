<template>
  <div>
    <div v-if="loading" class="loading">Loading...</div>

    <div v-if="!loading" class="articles">
      <div class="article" v-for="article of articles" v-bind:key="article.url">
        <div class="score">{{ article.score }}</div>
        <a class="title" target="_blank" v-bind:href="article.url">{{ article.title }}</a>
      </div>
    </div>

    <div v-if="error" class="error">{{ error }}</div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import axios from "axios";
import _ from "lodash/fp";

type Article = {
  by: string;
  descendants: number;
  id: number;
  kids: number[];
  score: number;
  time: number;
  title: string;
  type: "story" | string;
  url: string;
};

@Component
export default class NewsFeed extends Vue {
  @Prop() private msg!: string;

  data(): {
    loading: boolean;
    articles?: Article[];
    error: any;
  } {
    return {
      loading: false,
      articles: [],
      error: null,
    };
  }

  private watch: any = {
    // call again the method if the route changes
    $route: "fetchData",
  };

  async created() {
    const response = await axios.get(
      "https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty"
    );
    const { data } = response;
    const articles: Article[] = [];
    Promise.all(_.take(200)(data)
      .map(async (id: number) => {
        const response = await axios.get(
          `https://hacker-news.firebaseio.com/v0/item/${id}.json?print=pretty`
        );
        return response.data;
      }))
      .then((articles) => {
        const byScore = _.compose(_.reverse, _.sortBy("score"));
        const top = _.take(20);
        this.articles = top(byScore(articles));
      });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.articles {
  padding: 3rem 25%;
  text-align: left;

  display: grid;
  row-gap: 24px;
  column-gap: 24px;

  .article {
    padding: 0.25rem;

    .title {
      color: #FF6701;
    }
  }
}

</style>

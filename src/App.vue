<template>
  <!-- Application structure using Vuetify components -->
  <v-app>

    <!-- Navigation bar component -->
    <AppBar></AppBar>

    <v-main>
      <v-container class="bg-surface-variant">

        <!-- Main title of the application -->
        <v-row class="text-center ma-4">
          <v-col>
            <h1 class="display-1 font-weight-bold">Coding News hub: Articles from Hacker news</h1>
          </v-col>
        </v-row>

        <!-- Filtering and sorting options -->
        <v-row align="center" justify="center">
          <v-col cols="auto">
            <v-btn @click="refresh()">refresh</v-btn> <!-- Button to refresh articles -->
          </v-col>
          <v-col cols="auto">
            <v-switch hide-details class="my-0" v-model="includeArticlesWithoutUrl"
              :label="`Include articles without URL`"></v-switch> <!-- Switch to include articles without URL -->
          </v-col>
          <v-col cols="auto">
            <v-select hide-details label="Order by" v-model="order" item-text="text" item-value="value" :items="[
              { text: 'Oldest', value: 'asc' },
              { text: 'Most Recent', value: 'desc' }
            ]" outlined></v-select> <!-- Selector to order articles by Oldest or Most Recent -->
          </v-col>
        </v-row>

        <!-- Error message section -->
        <section v-if="errored">
          <div class="text-center ma-4">
            <v-icon color="red">mdi-alert-circle-outline</v-icon> <!-- Show an error icon if data cannot be retrieved -->
            <br>
            Sorry, it's not possible to retrieve the information at the moment, please try again later
          </div>
        </section>


        <!-- Articles section -->
        <section v-else>
          <!-- Loading message -->
          <div v-if="loading" class="text-center ma-4">
            Loading data... <br>
            <v-progress-circular indeterminate color="primary"></v-progress-circular>
            <!-- Show a progress circle while data is being loaded -->
          </div>


          <!-- Message when no articles are found -->
          <v-row class="text-center ma-4" v-else-if="!filteredArticles.length">
            <v-col>
              No articles found
            </v-col>
          </v-row>

          <!-- Article cards -->
          <v-row class="my-4" v-else>
            <v-col md="6" cols="12" class="pa-3 d-flex flex-column" v-for="(article, index) in filteredArticles"
              :key="index">
              <article-card  :article="article" @showModal="showModal"></article-card>
              <!-- Component to display each article card, passing the article and listening to the showModal event -->
            </v-col>
          </v-row>

          <!-- Component to show the detailed view of the selected article in a modal -->
          <article-modal :selectedArticle="selectedArticle" :modalOpen.sync="modalOpen"></article-modal>
        </section>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import axios from 'axios';
import { defineAsyncComponent } from 'vue';
import AppBar from './components/AppBar.vue';

// Define components for lazy loading
const ArticleCard = defineAsyncComponent(() => import('./components/ArticleCard.vue'));
const ArticleModal = defineAsyncComponent(() => import('./components/ArticleModal.vue'));

export default {
  name: 'App',

  components: {
    AppBar,
    ArticleCard,
    ArticleModal,
  },

  data() {
    return {
      articles: [], // Stores articles obtained from the API
      selectedArticle: null, // Stores the selected article to show in the modal
      includeArticlesWithoutUrl: false, // Controls whether to include articles without URL in the list
      modalOpen: false, // Controls whether the modal is open or closed
      loading: true, // Controls the visibility of the progress circle while data is being fetched
      errored: false, // Controls the visibility of the error message if data cannot be fetched
      order: 'desc', // Stores the selected order for the list of articles (oldest or most recent)
    };
  },

  computed: {
    // Filters and sorts the articles based on the selected options
    filteredArticles() {
      let filtered = this.includeArticlesWithoutUrl ? this.articles : this.articles.filter((article) => article.story_url);

      if (this.order == 'asc') {
        // Sort articles by ascending date of creation
        filtered.sort((a, b) => new Date(a.created_at) - new Date(b.created_at));
      } else {
        // Sort articles by descending date of creation
        filtered.sort((a, b) => new Date(b.created_at) - new Date(a.created_at));
      }

      return filtered;
    },
  },

  methods: {
    // Fetches data from the Hacker News API using Axios
    async fetchData() {
      try {
        const response = await axios.get('https://hn.algolia.com/api/v1/search_by_date?query=coding');
        this.articles = response.data.hits.map((article) => ({
          ...article,
          formattedDate: new Date(article.created_at).toLocaleString(),
        }));
        this.errored = false;
        console.log(this.articles);
      } catch (error) {
        console.error('Error fetching data:', error);
        this.errored = true;
      } finally {
        this.loading = false;
      }
    },

    // Method to refresh data (calls fetchData)
    refresh() {
      this.loading = true;
      this.fetchData();
    },

    // Method to show the modal with the selected article
    showModal(article) {
      this.selectedArticle = article;
      this.modalOpen = true;
      console.log(this.selectedArticle);
    },

  },

  // Fetches data when the component is mounted
  mounted() {
    this.fetchData();
  },
};
</script>

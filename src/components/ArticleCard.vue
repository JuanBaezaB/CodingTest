<template>
    <!-- Vuetify v-card component used to display the article card -->
    <v-card outlined elevation="3" class="flex d-flex flex-column">
        <!-- Display the date of the article -->
        <v-card-text class="pb-0 body-1">
            {{ article.formattedDate }}
        </v-card-text>

        <!-- Display the title of the article -->
        <v-card-title class="text-h5 font-weight-bold">
            {{ article.story_title }}
        </v-card-title>

        <!-- Display the author of the article -->
        <v-card-subtitle class="pb-0 body-1">
            {{ article.author }}
        </v-card-subtitle>

        <!-- Display the URL of the article with a link -->
        <v-card-text class="flex">
            <a class="body-1" :href="article.story_url">{{ article.story_url }}</a>
        </v-card-text>

        <!-- Action buttons for the article card -->
        <v-card-actions class="mb-3 ml-3">
            <!-- Button to show article details, triggers the 'showModal' method -->
            <v-btn variant="text" color="teal-accent-4" @click="showModal(article)">
                Details
            </v-btn>
        </v-card-actions>
    </v-card>
</template>

<script>
export default {
    props: ['article'], // Receiving the 'article' prop from the parent component

    methods: {
        // Method to show the modal with the selected article details
        showModal(article) {
            this.$emit('showModal', article); // Emits the 'showModal' event to the parent component with the selected article
        }
    }
}
</script>

<template>
  <!-- Modal dialog component using Vuetify v-dialog -->
  <v-dialog width="auto" max-width="800" v-model="isModalOpen">
    <v-card v-if="selectedArticle">
      <!-- Display the title of the selected article -->
      <v-card-title class="font-weight-bold text-h5">{{ selectedArticle.story_title }}</v-card-title>

      <!-- Display the comment text of the selected article as HTML content -->
      <v-card-text class="body-1" v-html="selectedArticle.comment_text"></v-card-text>

      <!-- Display the tags of the selected article in a chip group -->
      <v-card-actions>
        <v-chip-group>
          <v-chip v-for="(tag) in selectedArticle._tags" :key="tag">{{ tag }}</v-chip>
        </v-chip-group>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  props: ['selectedArticle', 'modalOpen'], // Receiving two props: 'selectedArticle' and 'modalOpen'

  computed: {
    // Computed property to synchronize the 'modalOpen' prop with 'isModalOpen'
    isModalOpen: {
      get() {
        return this.modalOpen;
      },
      set(value) {
        this.$emit('update:modalOpen', value);
      },
    },
  },
};
</script>

<template>
  <div class="mx-auto text-center">
    <v-text-field
        v-model="searchQuery"
        clearable
        label="Search by title"
        @keydown.enter="fetchPosts(1)"
        :disabled="loading"
        class="no-bottom-border search-field"
    >
      <template #append>
        <v-btn :loading="loading" size="small" color="primary" @click="fetchPosts(1)">search</v-btn>
      </template>
    </v-text-field>

    <v-overlay :value="loading">
      <v-progress-circular indeterminate size="64"></v-progress-circular>
    </v-overlay>

    <div v-if="!loading && searchQuery && !filteredPosts.length" class="text-error mt-4">
      Nothing found.
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "SearchBar",
  props: {
    loading: Boolean,
    fetchPosts: Function,
    searchQuery: String,
    filteredPosts: Array,
  },
  data() {
    return {
      searchQuery: this.search
    }
  },
  methods: {
    indexLog() {
      console.log('its me postiten index vue')
    },

  },
  mounted() {
    // console.log(this.filteredPosts)
    // this.$parent.parentLog(); получаем инфу от родетлеля
    //  this.$parent.$refs.test.indexLoger(); // получаем инфу от дочернего в дочернем

  },
  watch: {
    searchQuery(val) {
      this.$emit('update-search-query', val)
      // console.log(val)
    }
  },
};
</script>

<style scoped>
.no-bottom-border {
  border-bottom: none;
}

.search-field {
  width: 550px;
}
</style>

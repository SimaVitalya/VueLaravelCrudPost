<template>
  <v-card :key="post.id" class="post-card">
    <v-card-title>
      <v-text-field v-model="post.title" :readonly="!post.editing" class="post-title"></v-text-field>
    </v-card-title>
    <v-card-text>
      <v-textarea v-model="post.content" :readonly="!post.editing" class="post-content"></v-textarea>
    </v-card-text>
    <v-card-actions>
      <v-btn @click.prevent="toggleEdit(post)" size="small" color="blue">
        {{ post.editing ? 'Save' : 'Edit' }}
      </v-btn>
      <v-btn @click.prevent="deletePost(post)" size="small" color="error">Delete</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
import axios from "axios";

export default {
  props: {
    post: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      test: 'test',
      loading: false
    };
  },
  mounted() {
    // this.$parent.parentLog(); проверяем получаем ли мы доступ к родительской функции
  },

  methods: {
    toggleEdit(post) {
      if (post.editing) {
        axios
            .put(`api/post/update/${post.id}`, {
              title: post.title,
              content: post.content
            })
            .then((res) => {
              console.log(res);
            });
      }
      post.editing = !post.editing;
    },

    deletePost(post) {
      console.log('test')
      const currentPage = this.$parent.page;
      axios.delete(`api/post/delete/${post.id}`).then((res) => {
        this.$parent.fetchPosts(currentPage).then(() => {
          if (
              this.$parent.filteredPosts.length === 0 &&
              this.$parent.page > 1
          ) {
            this.$parent.page--;
            this.$parent.fetchPosts(this.$parent.page);
          }
        });
        console.log(res);
      });
    }
  }
};
</script>

<style scoped>
.post-card {
  margin-bottom: 20px;
}

.post-title {
  font-size: 20px;
  font-weight: bold;
}

.post-content {
  min-height: 100px;
}
</style>

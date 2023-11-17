<template>
  <div>
    <v-text-field v-model="newPost.title" label="Title"></v-text-field>
    <v-textarea v-model="newPost.content" label="Content"></v-textarea>
    <v-btn class="mb-15" @click.prevent="storePost" size="small" color="success">Add post</v-btn>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "AddPostForm",
  data() {
    return {
      newPost: {
        title: "",
        content: ""
      }
    };
  },
  methods: {
    storePost() {
      axios
          .post("api/post/store", {
            title: this.newPost.title,
            content: this.newPost.content
          })
          .then((res) => {
            this.newPost.title = "";
            this.newPost.content = "";
            this.$emit("post-added");
          })
          .catch((error) => {
            console.error(error);
          });
    }
  }
};
</script>

<style scoped>

</style>

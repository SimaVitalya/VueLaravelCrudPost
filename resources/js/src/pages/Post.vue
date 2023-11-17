<template>
  <div>
    <post-form @post-added="fetchPosts"></post-form>
    <div class="d-flex justify-content-between">
      <sort-menu :loading="loading" :sort-items="sortItems" :active-sort-title="activeSortTitle"
                 @sort="sort">

      </sort-menu>
      <div>
        <div>
          <search-bar
              :loading="loading"
              :value="searchQuery"
              :fetch-posts="fetchPosts"
              :filtered-posts="filteredPosts"
              @searchQuery="searchQuery =$event"
          ></search-bar>
          <!--          <p>{{ searchQuery }} проверям связь межку компонентами </p>-->
        </div>
      </div>
    </div>
    <PostItem
        v-for="post in paginatedPosts"
        :key="post.id"
        :post="post"
    ></PostItem>

    <v-pagination
        class="mt-10"
        :total-visible="6"
        color="error"
        v-model="page"
        :length="totalPages"
        v-if="totalPages > 1"
    ></v-pagination>


  </div>
</template>

<script>
import axios from "axios";
import PostForm from "@/pages/PostForm.vue";
import SortMenu from "@/pages/SortMenu.vue";
import PostItem from "@/pages/PostItem.vue";
import SearchBar from "@/pages/SearchBar.vue";

export default {
  name: "Posts",
  components: {
    PostForm,
    SortMenu,
    PostItem,
    SearchBar,
  },

  data() {
    return {
      loading: false,
      searchQuery: '',
      totalPages: 0,
      newPost: {
        title: "",
        content: ""
      },
      posts: [],
      items: [],
      filteredPosts: [],
      sortItems: [
        {title: 'Стандартная сортировка'},
        {title: "Сортировать по имени от а до я"},
        {title: "Сортировать по имени от я до а"},
        {title: "Сортировать по дате >"},
        {title: "Сортировать по дате <"},
      ],
      activeSortTitle: 'Стандартная сортировка',
      order_by: 'title',
      sort_by: 'asc',
      perPage: 10,
      page: 1,
      current: 1,
      length: 10,
    };
  },
  mounted() {
    this.activeSortTitle = 'Стандартная сортировка';
    this.order_by = 'date';
    this.sort_by = 'desc';
    this.fetchPosts();

  },

  methods: {
    async fetchPosts(page = 1) {
      this.loading = true;
      try {
        const response = await axios.post('/api/post', {
          'order_by': this.order_by,
          'sort_by': this.sort_by,
          'search_query': this.searchQuery,
          'page': page,
          'per_page': this.perPage,
        });
        // console.log(response.data.posts);
        this.items = response.data.posts.data;
        this.filteredPosts = response.data.posts.data;
        this.totalPages = response.data.posts.last_page;

      } catch (error) {
        console.log(error);
      } finally {
        this.loading = false;
      }
    },
    sort(item) {
      if (item.title === "Стандартная сортировка") {
        this.activeSortTitle = "Стандартная сортировка";
        this.order_by = "date";
        this.sort_by = "desc";
      } else if (item.title === "Сортировать по имени от я до а") {
        this.activeSortTitle = "Сортировать по имени от я до а";
        this.order_by = "title";
        this.sort_by = "desc";
      } else if (item.title === "Сортировать по имени от а до я") {
        this.activeSortTitle = "Сортировать по имени от а до я";
        this.order_by = "title";
        this.sort_by = "asc";
      } else if (item.title === "Сортировать по дате >") {
        this.activeSortTitle = "Сортировать по дате >";
        this.order_by = "date";
        this.sort_by = "desc";
      } else if (item.title === "Сортировать по дате <") {
        this.activeSortTitle = "Сортировать по дате >";
        this.order_by = "date";
        this.sort_by = "asc";
      }
      this.fetchPosts();
    },
    storePost() {
      axios
          .post("api/post/store", {
            title: this.newPost.title,
            content: this.newPost.content

          })
          .then((res) => {
            this.newPost.title = "";
            this.newPost.content = "";
            this.fetchPosts().then(() => {
              this.page = 1
            });
          });
    },
    toggleEdit(post) {
      if (post.editing) {

        axios.put(`api/post/update/${post.id}`, {
          title: post.title,
          content: post.content
        }).then((res) => {
          console.log(res);
        });
      }
      post.editing = !post.editing;
    },
    deletePost(post) {
      const currentPage = this.page;
      axios.delete(`api/post/delete/${post.id}`).then((res) => {
        this.fetchPosts(currentPage).then(() => {
          if (this.filteredPosts.length === 0 && this.page > 1) {
            this.page--;
            this.fetchPosts(this.page);
          }
        });
        console.log(res);
      });
    },

  },
  computed: {
    paginatedPosts() {
      const start = (this.current - 1) * this.length;
      const end = start + this.length;
      return this.filteredPosts.slice(start, end);

    }
  },
  watch: {
    page(newPage, oldPage) {
      if (newPage !== oldPage) {
        this.fetchPosts(newPage);
      }
    }
  }
}
;
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

.no-bottom-border {
  border-bottom: none;
}

.search-field {
  width: 550px;

}
</style>

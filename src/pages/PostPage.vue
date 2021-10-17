<template lang="">
    <div>  
      <h1>Page with posts</h1> 
      <my-input 
      v-model="searchQuery"
      placeholder="Search..."
      v-focus
      > 
      </my-input>
      <div class="app__btns">     
        <my-button 
        @click="showDialog" 
        >
        Create a post
        </my-button>
        <my-select 
        v-model="selectedSort"
        :options = "sortOptions"
        />     
      </div>
      <my-dialog v-model:show="dialogVisible">
        <post-form @create='createPost'/>
      </my-dialog> 
      <post-list 
      :posts="sortedAndSearchedPosts" 
      @remove='removePost'
      v-if="!isPostLoading"
      /> 
      <div v-else>
        Loading...
      </div>
      <div v-intersection="loadMorePosts" class="observer"></div>
      <!-- <div class="page__wrapper">
        <div 
        v-for='pageNumber in totalPages'
        :key="pageNumber"
        class="page"
        :class="{
          'current-page': page === pageNumber 
        }" 
        @click="changePage(pageNumber)"       
        >
          {{ pageNumber }}
        </div>
      </div> -->
    </div>    
</template>
<script>
import PostForm from "@/components/PostForm";
import PostList from "@/components/PostList";
import axios from "axios";
export default {
  components: {
    PostForm,
    PostList,
  },
  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostLoading: false,
      selectedSort: "",
      sortOptions: [
        { value: "title", name: "By name" },
        { value: "body", name: "By description" },
      ],
      searchQuery: "",
      page: 1,
      limit: 10,
      totalPages: 0,
    };
  },
  methods: {
    createPost(post) {
      this.posts.unshift(post);
      this.dialogVisible = false;
    },
    removePost(post) {
      this.posts = this.posts.filter((p) => p.id !== post.id);
    },
    showDialog() {
      this.dialogVisible = true;
    },
    async fetchPosts() {
      try {
        this.isPostLoading = true;
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts",
          {
            params: {
              _page: this.page,
              _limit: this.limit,
            },
          }
        );
        this.totalPages = Math.ceil(
          response.headers["x-total-count"] / this.limit
        );
        this.posts = response.data;
      } catch (error) {
        alert("Error");
      } finally {
        this.isPostLoading = false;
      }
    },
    async loadMorePosts() {
      try {
        this.page += 1;
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts",
          {
            params: {
              _page: this.page,
              _limit: this.limit,
            },
          }
        );
        this.totalPages = Math.ceil(
          response.headers["x-total-count"] / this.limit
        );
        this.posts = [...this.posts, ...response.data];
      } catch (error) {
        alert("Error");
      }
    },
    // changePage(pageNumber) {
    //   this.page = pageNumber;
    // },
  },
  mounted() {
    this.fetchPosts();    
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) =>
        post1[this.selectedSort]?.localeCompare(post2[this.selectedSort])
      );
    },
    sortedAndSearchedPosts() {
      return this.sortedPosts.filter((post) =>
        post.title.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
  watch: {
    // page() {
    //   this.fetchPosts();
    // },
  },
};
</script >
<style>
.app__btns {
  margin: 15px 0;
  display: flex;
  justify-content: space-between;
}
.page__wrapper {
  display: flex;
  margin-top: 15px;
}
.page {
  border: 1px solid black;
  padding: 10px;
}
.current-page {
  border: 2px solid teal;
}
.observer {
  height: 30px;
  background: green;
}
</style>
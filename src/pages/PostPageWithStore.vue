<template lang="">
    <div>  
      <h1>{{$store.state.isAuth? "User authenticated...":"User not authenticated..."}}</h1>
      <h1>{{$store.getters.doubleLikes}}</h1>
      <div>
        <my-button @click="$store.commit('incrementLikes')">Like</my-button>
        <my-button @click="$store.commit('decrementLikes')">Dislike</my-button>
      </div>
      <h1>Page with posts</h1> 
      <my-input 
      :model-value="searchQuery"
      @update:model-value='setSearchQuery'
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
        :model-value="selectedSort"
        @update:model-value='setSelectedSort'
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
    </div>    
</template>
<script>
import PostForm from "@/components/PostForm";
import PostList from "@/components/PostList";
import MyButton from "@/components/UI/MyButton";
import {mapState, mapGetters, mapActions, mapMutations} from 'vuex';
import axios from "axios";
export default {
  components: {
    PostForm,
    PostList,
    MyButton
  },
  data() {
    return {
      // posts: [],
      dialogVisible: false,
      // isPostLoading: false,
      // selectedSort: "",
      // sortOptions: [
      //   { value: "title", name: "By name" },
      //   { value: "body", name: "By description" },
      // ],
      // searchQuery: "",
      // page: 1,
      // limit: 10,
      // totalPages: 0,
    };
  },
  methods: {
    ...mapMutations({
      setPage: 'post/setPage',
      setSearchQuery: 'post/setSearchQuery',
      setSelectedSort: 'post/setSelectedSort',
    }),
    ...mapActions({
      loadMorePosts: 'post/loadMorePosts',
      fetchPosts: 'post/fetchPosts',
    }),
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
  },
  mounted() {
    this.fetchPosts();
  },
  computed: {
    ...mapState({
      posts: state => state.post.posts,
      isPostsLoading: state => state.post.isPostsLoading,
      selectedSort: state => state.post.selectedSort,
      searchQuery: state => state.post.searchQuery,
      page: state => state.post.page,
      limit: state => state.post.limit,
      totalPages: state => state.post.totalPages,
      sortOptions: state => state.post.sortOptions    
    }),
    ...mapGetters({
      sortedPosts: 'post/sortedPosts',
      sortedAndSearchedPosts: 'post/sortedAndSearchedPosts'
    })
    // sortedPosts() {
    //   return [...this.posts].sort((post1, post2) =>
    //     post1[this.selectedSort]?.localeCompare(post2[this.selectedSort])
    //   );
    // },
    // sortedAndSearchedPosts() {
    //   return this.sortedPosts.filter((post) =>
    //     post.title.toLowerCase().includes(this.searchQuery.toLowerCase())
    //   );
    // },
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
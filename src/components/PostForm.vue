<template lang="">
  <form @submit.prevent>
        <h4>Post creation</h4>
        <my-input 
        v-focus
        v-model="post.title"
        placeholder="Name"
        />
        <my-input 
        v-model="post.body"
        placeholder="Description"
        />
        <my-button 
        style="align-self: flex-end; margin-top: 15px"
        @click="createPost">
        Create
        </my-button>
      </form>
</template>
<script>
export default {
  data() {
    return {
      post: {
        title: "",
        body: "",
      },
    };
  },
  watch: {
    // post: {
    //   handler(newValue) {
    //     console.log(newValue);
    //   },
    //   deep: true,
    // },
  },
  methods: {
    createPost() {
      this.post.id = Date.now();
      this.$emit("create", this.post);
      this.post = {
        title: "",
        body: "",
      };
    },
  },
};
</script>
<style scoped>
form {
  display: flex;
  flex-direction: column;
}
</style>
<template>
  <div class="container">
    <nav class="nav-bar">
      <div class="nav-container">
        <span class="nav-brand">NewsFeed</span>
        <ul class="nav-links">
          <li class="nav-item">
            <router-link class="nav-link" to="/logout">Logout</router-link>
          </li>
        </ul>
      </div>
    </nav>
    <router-link to="/posts/create" class="btn btn-primary">Create New Post</router-link>
    <br>
    <br>
    <table class="table-striped">
      <thead>
        <tr>
          <th width="25%">Title</th>
          <th width="30%">Author</th>
          <th width="20%">Created At</th>
          <th width="25%">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in posts" :key="post.id">
          <td>{{ post.title }}</td>
          <td>{{ post.user.name }}</td>
          <td>{{ formatDate(post.created_at) }}</td>
          <td>
            <router-link :to="{ name: 'show', params: { id: post.id } }" class="btn btn-primary mr-2 btn-sm">View</router-link>
            <router-link :to="{ name: 'edit', params: { id: post.id } }" class="btn btn-secondary mr-2 btn-sm">Edit</router-link>
            <button @click="deletePost(post.id)" class="btn btn-danger mr-2 btn-sm">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'PostIndex',
  data() {
    return {
      posts: [],
    };
  },
  created() {
    this.fetchPosts();
  },
  methods: {
    fetchPosts() {
      axios.get('/api/posts')
        .then(response => {
          this.posts = response.data;
        })
        .catch(error => {
          console.error("There was an error fetching the posts:", error);
        });
    },
    formatDate(date) {
      const options = { year: 'numeric', month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit', second: '2-digit' };
      return new Date(date).toLocaleDateString('en-US', options);
    },
    deletePost(postId) {
      if (confirm('Are you sure you want to delete this post?')) {
        axios.delete(`/api/posts/${postId}`)
          .then(response => {
            this.posts = this.posts.filter(post => post.id !== postId);
          })
          .catch(error => {
            console.error("There was an error deleting the post:", error);
          });
      }
    },
  },
};
</script>

<style scoped>
.container {
  padding: 20px;
}
.table-striped {
  width: 100%;
  border-collapse: collapse;
}
.table-striped th, .table-striped td {
  border: 1px solid #ddd;
  padding: 8px;
}
.table-striped tr:nth-child(even) {
  background-color: #f2f2f2;
}
.table-striped th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #4CAF50;
  color: white;
}

.nav-bar {
  background-color: #33f6fa;
  color: rgb(15, 0, 0);
  padding: 10px 0;
}

.nav-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.nav-brand {
  font-size: 1.5rem;
  margin-left: 20px;
}

.nav-links {
  list-style: none;
  margin-right: 20px;
}

.nav-item {
  display: inline-block;
  margin-left: 20px;
}

.nav-link {
  color: rgb(218, 36, 36);
  text-decoration: none;
  font-weight: bold;
}

.nav-link:hover {
  text-decoration: underline;
}
</style>

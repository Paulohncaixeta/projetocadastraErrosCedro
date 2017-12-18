<template>
  <div>
    <ul v-if="posts && posts.length">
      <li v-for="post of posts" :key="post.id">
        <p><strong>{{post.title}}</strong></p>
        <p>{{post.body}}</p>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      posts: [],
      errors: []
    }
  },

  
  created() {
    var config = {
      headers:{
        'Access-Control-Allow-Origin':'*'
      }
    }
    axios.get('http://dev.people.com.ai/bug/api/v3/products', config)
    .then(response => {
    
      this.posts = response.data
    })
    .catch(e => {
      this.errors.push(e)
    })
    console.log(this.posts)

  }
}
</script>
<template>
<div class="home">
  <div class="home">
  <section class="image-gallery" v-if="items.length > 0">
    <div class="image" v-for="item in items" :key="item.id">
      <h2>{{item.title}}</h2>
      <h2>{{item.author}}</h2>
      <h2>{{item.genre}}</h2>
      <img :src="item.path" />
      <p class="descript">-{{item.description}}</p>
    </div>
  </section>
  <div id="no-books" v-else> 
    <h3>No Books Added. Go to Admin in the footer and add books.</h3>
  </div>
</div>
</div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios';
export default {
  name: 'Home',
  data() {
  return {
   items: [],
  }
},
created() {
  this.getItems();
},
methods: {
  async getItems() {
    try {
      let response = await axios.get("/api/items");
      this.items = response.data;
      return true;
    } catch (error) {
      console.log(error);
    }
  },
}
}
</script>

<style scoped>
.image h2 {
  font-style: italic;
}

.descript{
  font-family: monospace;
}
/* Masonry */
*,
*:before,
*:after {
  box-sizing: inherit;
}

.image-gallery {
  column-gap: 1.5em;
}

.image {
  margin: 0 0 1.5em;
  display: inline-block;
  width: 100%;
}

.image img {
  width: 100%;
}
#no-books{
  text-align: center;
}

/* Masonry on large screens */
@media only screen and (min-width: 1024px) {
  .image-gallery {
    column-count: 4;
    margin: auto;
  }
  
}

/* Masonry on medium-sized screens */
@media only screen and (max-width: 1023px) and (min-width: 768px) {
  .image-gallery {
    column-count: 3;
  }
}

/* Masonry on small screens */
@media only screen and (max-width: 767px) and (min-width: 540px) {
  .image-gallery {
    column-count: 2;
  }
}
</style>

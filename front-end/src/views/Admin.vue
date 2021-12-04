<template>
<div class="admin">
  <h1>Books Edit Page!</h1>
<div class="heading">
  <div class="circle">1</div>
  <h2>Add a book</h2>
</div>
<div class="firstFunc">
  <div class="add">
    <div class="form">
      <p><b>Title</b></p>
      <input v-model="title" placeholder="Title">
      <p><b>Author</b></p>
      <input v-model="author" placeholder="Author">
      <p><b>Genre</b></p>
      <input v-model="genre" placeholder="Genre">
      <p><b>Description</b></p>
      <textarea v-model = "description" placeholder = "Description"
                rows="5" cols="33">
      </textarea>
      <input type="file" name="photo" @change="fileChanged">
      <button @click="upload">Upload</button>
    </div>
  </div>
  <div class="upload" v-if="addItem">
    <h1>Title:  {{addItem.title}}</h1>
    <h1>Author:  {{addItem.author}}</h1>
    <h1>Genre:  {{addItem.genre}}</h1>
    <h1>Description: <br>{{addItem.description}}</h1>
    <img :src="addItem.path" />
  </div>
</div>

<div class="heading">
  <div class="circle">2</div>
  <h2>Edit/Delete a book</h2>
</div>
<div class="edit">
  <div class="form">
    <input v-model="findTitle" placeholder="Search">
    <div class="suggestions" v-if="suggestions.length > 0">
      <div class="suggestion" v-for="s in suggestions" :key="s.id" @click="selectItem(s)">{{s.title}}
      </div>
    </div>
  </div>
  <div class="upload" v-if="findItem">
    <p><b>Title</b></p>
    <input v-model="findItem.title">
    <p><b>Author</b></p>
    <input v-model="findItem.author">
    <p><b>Genre</b></p>
    <input v-model="findItem.genre">
    <p><b>Description</b></p>
    <textarea v-model = "findItem.description" placeholder = "Description"
              rows="5" cols="33">
    </textarea>
    <p></p>
  </div>
  <div class="actions" v-if="findItem">
    <img :src="findItem.path" />
    <button @click="editItem(findItem)">Edit</button>
    <button @click="deleteItem(findItem)">Delete</button>
  </div>
</div>
</div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'Admin',
  data() {
  return {
    title: "",
    file: null,
    addItem: null,
    items: [],
    findTitle: "",
    findItem: null,
    description: "",
    author: "",
    genre:"", 
  }
},
created() {
  this.getItems();
},
computed: {
  suggestions() {
    let items = this.items.filter(item => item.title.toLowerCase().startsWith(this.findTitle.toLowerCase()));
    return items.sort((a, b) => a.title > b.title);
  }
},
methods: {
  fileChanged(event) {
    this.file = event.target.files[0]
  },
  selectItem(item) {
  this.findTitle = "";
  this.findItem = item;
},
  async upload() {
  try {
    const formData = new FormData();
    formData.append('photo', this.file, this.file.name)
    let r1 = await axios.post('/api/photos', formData);
    let r2 = await axios.post('/api/items', {
      title: this.title,
      author: this.author,
      genre: this.genre,
      description: this.description,
      path: r1.data.path
    });
    this.addItem = r2.data;
  } catch (error) {
    console.log(error);
  }
},
async getItems() {
  try {
    let response = await axios.get("/api/items");
    this.items = response.data;
    return true;
  } catch (error) {
    console.log(error);
  }
},
async deleteItem(item) {
  try {
    await axios.delete("/api/items/" + item._id);
    this.findItem = null;
    this.getItems();
    return true;
  } catch (error) {
    console.log(error);
  }
},
async editItem(item) {
  try {
    await axios.put("/api/items/" + item._id, {
      title: this.findItem.title,
      author: this.findItem.author,
      genre: this.findItem.genre,
      description: this.findItem.description,
    });
    this.findItem = null;
    this.getItems();
    return true;
  } catch (error) {
    console.log(error);
  }
},
}
}
</script>


<style scoped>

.actions {
  display: flex;
  width: 380px; 
  align-items:flex-start;
  flex-direction: column;
  margin-left: 2vw;
}
.image h2 {
  font-style: italic;
  font-size: 1em;
}

.heading {
  display: flex;
  margin-bottom: 20px;
  margin-top: 20px;
}

.heading h2 {
  margin-top: 8px;
  margin-left: 10px;
}
.firstFunc {
  display: grid; 
  grid-template-columns: 1fr 1fr;
}
.add {
  display: grid; 
}
.edit {
  display: grid;
}

.circle {
  border-radius: 50%;
  width: 18px;
  height: 18px;
  padding: 8px;
  background: #333;
  color: #fff;
  text-align: center
}

/* Form */
input,
textarea,
select,
button {
  font-family: 'Montserrat', sans-serif;
  font-size: 1em; 
  height: 30px; 
  margin: 1vw; 
}

.form {
  display: grid; 
  margin-right: 50px;
}

/* Uploaded images */
.upload {
  display: grid; 
  
}
.upload h2 {
  margin: 1vw;
}

.upload img {
  max-width: 300px;
  max-height: 300px;
}

/* Suggestions */
.suggestions {
  width: 200px;
  border: 1px solid #ccc;
}

.suggestion {
  min-height: 20px;
}

.suggestion:hover {
  background-color: #5BDEFF;
  color: #fff;
}

</style>

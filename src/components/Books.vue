<template>
  <div class="container">
    <div class="card">
      <div class="heading">
        <h4 class="title">NYTimes Books</h4>
      </div>
      <div class="search">
        <input id="input-search" type="text" class="form-control" placeholder="Search by title" v-bind:class="{'block-cursor': !enableSearch}" :disabled="!enableSearch" @keyup.enter="searchBook($event.target.value)"/>
      </div>
      <div class="content">
        <!-- ADD CONTENT HERE -->
        <p>Please select category</p>
          <select id="select-category" @change="categorySelected($event.target.value)">
            <option value="" selected></option>
            <option :value=ele.list_name_encoded v-for="(ele,index) in categories" v-bind:key="index">{{ ele.list_name }}</option>
          </select>
          <br>
          <br>
          <div v-if="bookSearched">
            <table>
              <thead>
                  <th>Title</th>
                  <th>Description</th>
                  <th>Author</th>
              </thead>
              <tbody>
                 <a v-bind:href="'https://www.google.com/search?q=' + bookSearched.title + ' ' + bookSearched.author">
                  <tr>
                    <td>{{ bookSearched.title }}</td>
                    <td>{{ bookSearched.description }}</td>
                    <td>{{ bookSearched.author }}</td>
                  </tr>
                </a>
              </tbody>
            </table>
          </div>
          <div id="not-found" v-else-if="!bookSearched && title">
            The book was not found, try again...
          </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "Books",
  data() {
    return {
      categories : [],
      books: [],
      title: "",
      enableSearch: false,  
      bookSearched: null,
    }
  },

  methods: {
      categorySelected(e) {
        this.clearValues();
        if (e.length > 0) {
          this.enableSearch = true;
          this.searchBooksFromCategory(e);
        } else {
          this.enableSearch = false;
        }
      },

      clearValues() {
        this.title = "";
        this.bookSearched = null;
        document.getElementById('input-search').value = "";
      },

      searchBooksFromCategory(category) {   // busca los libros asociados a la categoria elegida.
        axios({
          method:'GET',
          url:"https://api.nytimes.com/svc/books/v3/lists/current/" + category + ".json?api-key=wMrIxYjKdpTQq76wy7ngPAG1OD0VJy8j"
        }).then(res => {
          this.books = res.data.results.books;
        });
      },

      searchBook(e) {
        this.title = e;
        if (this.title.length > 0) {
          this.bookSearched = this.books.find(ele => ele.title.toLowerCase() == this.title.toLowerCase());
        }
      }
  },

  mounted() {
      axios({
        method:'GET',
        url:"https://api.nytimes.com/svc/books/v3/lists/names.json?api-key=wMrIxYjKdpTQq76wy7ngPAG1OD0VJy8j"
      }).then(res => {
       this.categories = res.data.results.slice(0,10);
      });
  },
};
</script>

<style scoped lang="scss">
.container {
  z-index: 1;
  margin: 36px auto;
  max-width: 826px;
  background-color: white;
}

.card {
  box-shadow: 0 2px 3px rgba(10, 10, 10, 0.1), 0 0 0 1px rgba(10, 10, 10, 0.1);
  padding: 24px;
}

.heading {
  margin-bottom: 12px;

  .title {
    font-size: 18px;
    font-weight: 600;
  }
}

.search {
  margin-bottom: 24px;
  .form-control {
    background-color: transparent;
    border: none;
    border-bottom: 1px solid #ced4da;
    border-radius: 0;
    outline: 0;
    box-shadow: none;
    padding: 6px 0;
    width: 100%;
  }
}

.block-cursor {
  cursor: not-allowed;
}

#select-category {
  width: 50%;
  font-family: Arial, Helvetica, sans-serif;
  height: 25%;
  padding: 1px;
}

th, td {
  border: 1px solid #ddd;
  padding: 9px;
  font-size: 15px;
}

tr:hover{
  background-color:#FBEEE6;
  cursor:pointer;
} 

a {
  text-decoration: none;
  color: black !important;
  display: contents;
}

th {
  background-color: orange;
  color: white;
}

#not-found {
  font-size: 14px;
  color: red;
}
</style>

<template>
  <h1>Books:</h1>
  <div v-if="!creating">
    <button @click="toggleCreate">CREATE NEW BOOK</button>
    <ul>
      <li v-for="book in booksData" :key="book.id">
        {{ book.title }} by {{ book.author.name }} {{ book.author.surname }}
        <br />
        <h4>Bookshelfs:</h4>
        <ul>
          <li v-for="bookshelf in book.bookshelfs" :key="bookshelf.id">
            {{ bookshelf.name }} located in {{ bookshelf.address }}
          </li>
        </ul>
        <button @click="deleteBook(book.id)">CANCEL</button>
      </li>
    </ul>
  </div>
  <div v-else>
    <label>Title</label>
    <br />
    <input type="text" v-model="bookCreatingData.title" />
    <br />
    <br />
    <label>Author</label>
    <br />
    <select v-model="bookCreatingData.authorId">
      <option v-for="author in authorsData" :key="author.id" :value="author.id">
        {{ author.name }} {{ author.surname }}
      </option>
    </select>
    <br />
    <br />
    <label>Bookshelfs</label>
    <br />
    <div v-for="bookshelf in bookshelfsData" :key="bookshelf.id">
      <input
        type="checkbox"
        :id="'bs-' + bookshelf.id"
        :value="bookshelf.id"
        v-model="bookCreatingData.bookshelfIds"
      />
      <label :for="'bs-' + bookshelf.id"> {{ bookshelf.name }} in {{ bookshelf.address }} </label>
    </div>
    <br /><br />
    <button @click="toggleCreate">CANCEL</button>
    <button @click="createBook">SAVE</button>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

const authorsData = ref([])
const bookshelfsData = ref([])

const booksData = ref([])

const bookCreatingData = ref({
  title: '',
  authorId: '',
  bookshelfIds: []
})
const creating = ref(false)

const toggleCreate = () => {
  creating.value = !creating.value

  bookCreatingData.value = {
    title: '',
    authorId: '',
    bookshelfIds: []
  }
}

const createBook = () => {
  // console.log('Creating book:', JSON.stringify(bookCreatingData.value, null, 2))

  axios
    .post('http://localhost:8080/api/v1/books', bookCreatingData.value)
    .then((res) => {
      const data = res.data
      booksData.value.push(data)
      toggleCreate()
    })
    .catch((err) => console.error('Error: ' + err))
}
const deleteBook = (id) => {
  axios
    .delete('http://localhost:8080/api/v1/books/' + id)
    .then(() => {
      updateData()
    })
    .catch((err) => console.error('Error: ' + err))
}

const updateData = () => {
  axios
    .get('http://localhost:8080/api/v1/books')
    .then((res) => {
      const data = res.data
      booksData.value = data
    })
    .catch((err) => console.error('Error: ' + err))

  axios
    .get('http://localhost:8080/api/v1/authors')
    .then((res) => {
      const data = res.data
      authorsData.value = data
    })
    .catch((err) => console.error('Error: ' + err))

  axios
    .get('http://localhost:8080/api/v1/bookshelfs')
    .then((res) => {
      const data = res.data
      bookshelfsData.value = data
    })
    .catch((err) => console.error('Error: ' + err))
}

onMounted(updateData)
</script>

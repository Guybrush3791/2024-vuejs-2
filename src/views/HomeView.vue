<template>
  <h1>Books:</h1>
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
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

const booksData = ref([])

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
}

onMounted(updateData)
</script>

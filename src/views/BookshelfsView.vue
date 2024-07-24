<template>
  <h1>Bookshelfs:</h1>
  <ul>
    <li v-for="bookshelf in bookshelfsData" :key="bookshelf.id">
      {{ bookshelf.name }} in {{ bookshelf.address }}
      <ul>
        <li v-for="book in bookshelf.books" :key="book.id">
          {{ book.title }} by {{ book.author.name }} {{ book.author.surname }}
        </li>
      </ul>
    </li>
  </ul>
</template>
<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const bookshelfsData = ref([])

const updateData = async () => {
  axios
    .get('http://localhost:8080/api/v1/bookshelfs')
    .then((res) => {
      const data = res.data

      for (let i = 0; i < data.length; i++) {
        const bs = data[i]
        const bsId = bs.id

        axios.get('http://localhost:8080/api/v1/books/bookshelfs/' + bsId).then((res) => {
          const books = res.data
          bs.books = books

          console.log(data)
        })
      }

      bookshelfsData.value = data
    })
    .catch((err) => console.error('Error: ' + err))
}

onMounted(updateData)
</script>

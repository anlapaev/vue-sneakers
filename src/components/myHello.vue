<!-- <script>
export default {
    data() {
        return {
            count: 0
        }
    },
    methods: {
        increment() {
            this.count++
        },
        diincrement() {
            this.count--
        }
    }
}
</script> -->

<script setup>
import { ref } from 'vue'
const count = ref(0)

function increment() {
  count.value++
}
function dicrement() {
  count.value--
}

function updateCount(event) {
  count.value = Number(event.target.value)
}
</script>

<template>
  <h1>{{ count }}</h1>
  <button @click="increment">+</button>
  <button @click="dicrement">-</button>
  <input @input="updateCount" />
</template>

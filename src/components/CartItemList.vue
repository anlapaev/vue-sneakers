<script setup>
import { inject } from 'vue'
import CartItem from './CartItem.vue'

const { cart } = inject('cart')
</script>

<template>
  <div class="flex flex-col flex-1 gap-4">
    <CartItem v-for="item in cart" :key="item.id" :item="item" :title="item.title" :price="item.price" :image-url="item.imageUrl"/>
  </div>
</template>

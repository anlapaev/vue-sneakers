<script setup>
import { onMounted, ref, watch, reactive, provide } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

// items - массив кроссовок
const items = ref([])
const cart = ref([])

const drawerOpen = ref(false)

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const filters = reactive({
  sortBy: 'name',
  searchQuery: ''
})

const addToCart = (item) => {
  if (!item.isAdded) {
    cart.value.push(item)
    item.isAdded = true
  } else {
    cart.value = cart.value.splice(cart.value.indexOf(item), 1)
    item.isAdded = false
  }
}

// onChangeSelect нужен для изменения sortBy, смотрит его в html
const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
}

const fetchFavorite = async () => {
  try {
    const { data: favorites } = await axios.get('https://0f11cce2d7fb806b.mokky.dev/favorites')
    // items.value - массив кроссовок с сервера
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id)

      if (!favorite) {
        return item
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
  } catch (e) {
    console.log(e)
  }
}

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id
      }
      item.isFavorite = true
      const { data } = await axios.post('https://0f11cce2d7fb806b.mokky.dev/favorites', obj)

      item.favoriteId = data.id
      console.log(data)
    } else {
      item.isFavorite = false
      await axios.delete(`https://0f11cce2d7fb806b.mokky.dev/favorites/${item.favoriteId}`)

      item.favoriteId = null
    }
  } catch (e) {
    console.log(e)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    // если есть поисковый запрос выполняем поиск с сервера. params.title - значение поискового запроса
    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
    const { data } = await axios.get('https://0f11cce2d7fb806b.mokky.dev/items', { params })
    // items.value - массив кроссовок с сервера
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (e) {
    console.log(e)
  }
}

// onMounted запрашивает данные с сервера
onMounted(async () => {
  await fetchItems()
  await fetchFavorite()
})

// watch следит за изменениями в sortBy
watch(filters, fetchItems)

provide('cart', {
  cart,
  openDrawer,
  closeDrawer
})
</script>

<template>
  <Drawer v-if="drawerOpen" @close="drawerOpen = false" />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header @open-drawer="openDrawer" />

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

        <div class="flex gap-4">
          <select
            @change="onChangeSelect"
            class="py-2 px-3 border rounded-md outline-none"
            name=""
            id=""
          >
            <option value="name">По названию</option>
            <option value="price">По цене (возрастанию)</option>
            <option value="-price">По цене (убыванию)</option>
          </select>

          <div class="relative">
            <img class="absolute top-3 left-4" src="/search.svg" alt="Search" />
            <input
              @input="onChangeSearchInput"
              class="border border-gray-200 rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
              type="text"
              placeholder="Поиск...."
            />
          </div>
        </div>
      </div>

      <div class="mt-8">
        <!-- items - массив кроссовок -->
        <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="addToCart" />
      </div>
    </div>
  </div>
</template>

<style scoped></style>

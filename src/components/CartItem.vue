<script setup>
defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number,
})
</script>

<template>
  <div class="flex items-center border border-slate-200 rounded-xl p-4 gap-4">
    <img class="w-16 h-16" :src="imageUrl" :alt="title" />
    <div class="flex flex-col flex-grow">
      <p>{{ title }}</p>
      <div class="flex justify-between mt-2 w-full">
        <b>{{ price }} руб.</b>
        <img
          class="opacity-50 hover:opacity-100 cursor-pointer transition"
          src="/close.svg"
          alt="Close"
        />
      </div>
    </div>
  </div>
</template>

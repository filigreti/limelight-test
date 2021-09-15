<template>
  <div
    class="relative  rounded-md bg-gray-900 rounded-bl-none rounded-br-none bg-blend-darken w-full   h-64 "
  >
    <img
      v-if="items.image"
      :src="items.image"
      class=" w-full opacity-90   rounded-br-none rounded-bl-none   h-64 object-cover rounded-md"
    />

    <div
      class=" absolute flex items-center  text-gray-300 justify-between px-3 bottom-0 left-0 right-0  bg-black bg-opacity-50  py-2"
    >
      <div class="text-sm">
        {{ items.name }}
      </div>
      <div class="text-sm">${{ items.price }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["items"],
};
</script>

<style></style>

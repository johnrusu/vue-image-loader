<script setup lang="ts">
import { defineAsyncComponent, ref } from 'vue';
// utls
import { isNilOrEmpty } from './helpers';

const ImageLoader = defineAsyncComponent(() => import('./components/ImageLoader.vue'));
// Make sure that src/components/ImageLoader.vue exists, or update the path if needed.
const imageSrc = ref(null);
</script>

<template>
  <div class="container m-auto p-10 w-xl">
    <div class="header mb-10 font-bold text-3xl">Vue Image Loader</div>
    <div class="flex flex-row gap-20 flex-1">
      <fieldset class="w-full">
        <legend>Image Url:</legend>
        <div class="mt-2 w-full">
          <input
            type="text"
            id="imageSrc"
            class="border-2 border-gray-300 p-2 w-full"
            v-model="imageSrc"
          />
        </div>
      </fieldset>
      <fieldset class="w-full" v-if="!isNilOrEmpty(imageSrc)">
        <legend>Image Preview:</legend>
        <div class="mt-2">
          <ImageLoader :imageSrc="imageSrc" />
        </div>
      </fieldset>
    </div>
  </div>
</template>

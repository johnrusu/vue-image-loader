<script setup lang="ts">
import { defineProps, ref, watchEffect, useTemplateRef } from 'vue';

// utils
import { isNilOrEmpty, checkImage } from '@/helpers';

// constants
import { STATUS_IMAGE } from '@/constants/common';
import { is } from 'ramda';

const props = defineProps<{ imageSrc: string | null }>();
const imageError = ref(false);
const image = ref<string | null>(null);
const imageRefContainer = useTemplateRef<HTMLDivElement>('imageRefContainer');

const handleImageLoading = (
  statusLoadingImage: keyof typeof STATUS_IMAGE,
  imageSrc: string | null,
) => {
  const imageContainerElement = imageRefContainer.value;
  if (!isNilOrEmpty(imageContainerElement) && imageContainerElement instanceof HTMLDivElement) {
    image.value = imageSrc;
    switch (STATUS_IMAGE[statusLoadingImage]) {
      case STATUS_IMAGE.LOADING:
        imageContainerElement.classList.add(STATUS_IMAGE.LOADING);
        break;
      case STATUS_IMAGE.LOADED:
        imageContainerElement.classList.remove(STATUS_IMAGE.LOADING);
        break;
    }
  }
};

const loadImage = () => {
  const imageSrc: string = (props.imageSrc as string) || '';

  if (!isNilOrEmpty(imageSrc) && imageSrc && !isNilOrEmpty(imageRefContainer.value)) {
    imageError.value = false;
    handleImageLoading('LOADING', null);

    checkImage(imageSrc)
      .then((img) => {
        if (!isNilOrEmpty(img) && img?.src) {
          handleImageLoading('LOADED', img.src);
        } else {
          imageError.value = true;
          handleImageLoading('LOADED', null);
        }
      })
      .catch(() => {
        imageError.value = true;
        handleImageLoading('LOADED', null);
      });
  }
};

watchEffect(() => {
  if (!isNilOrEmpty(props.imageSrc) && is(String, props.imageSrc)) {
    loadImage();
  }
});
</script>
<template>
  <div v-if="imageError" class="text-red-500">Failed to load image.</div>
  <div v-else class="relative image-container" ref="imageRefContainer">
    <img v-if="!isNilOrEmpty(image)" :src="image ?? undefined" alt="Loaded Image" />
  </div>
</template>

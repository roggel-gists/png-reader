<template>
    <div class="p-4 flex flex-grow-1 gap-4 h-screen items-center justify-center text-center bg-gray-800">
        <ImagePreview :image="image" @reset="image = null" v-if="image" />
        <DropFile @dropped="setImage"/>
        <ImageDetails :image="image" v-if="image" />
    </div>
</template>

<script setup lang="ts">
    const image = ref<File | null>(null);
    const setImage = (droppedImage: File) => {
        image.value = droppedImage;
    }
</script>
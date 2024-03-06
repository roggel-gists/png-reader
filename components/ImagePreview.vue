<script setup lang="ts">
defineProps<{
    image: File
}>();
const generateURL = (file: File) => {
    if (!file) {
        return;
    }

    let fileSrc = URL.createObjectURL(file);
    setTimeout(() => {
        URL.revokeObjectURL(fileSrc)
    }, 1000)

    return fileSrc
}
</script>

<template>
    <div>
        <img :src="generateURL(image)" alt="your image" v-if="image" />
        <p class="text-gray-400" v-if="image" v-text="image.name" />
    </div>
</template>

<style scoped>
img {
    max-width: 40vw;
    max-height: 80vh;
    width: auto;
    height: auto;
}
</style>

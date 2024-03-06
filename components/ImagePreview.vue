<script setup lang="ts">
defineProps<{
    image: File
}>();
const emit = defineEmits(['reset']);
const generateURL = (file: File) => {
    if (!file || file.type !== 'image/png') {
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
    <div class="relative">
        <img
            :src="generateURL(image)"
            alt="your image"
            v-if="image"
            class="rounded-xl border-2 border-gray-400 shadow-2xl"
        />
        <div id="remove" @click="emit('reset')">
            <span>&times;</span>
        </div>
    </div>
</template>

<style scoped>
img {
    max-width: 40vw;
    max-height: 80vh;
    width: auto;
    height: auto;
}

#remove {
    @apply absolute top-1 right-1;
    @apply w-6 h-6;
    @apply flex justify-center items-center;
    @apply bg-white;
    @apply rounded-full border border-black;
    @apply cursor-pointer;
    line-height: 0;
}
</style>

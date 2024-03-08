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
            class="rounded-xl border-2 border-gray-600 shadow-2xl"
        />
        <div id="remove" @click="emit('reset')">
            <span>&times;</span>
        </div>
    </div>
</template>

<style scoped>
img {
    @apply max-w-[40vw] max-h-[80vh];
    @apply w-auto h-auto;
}

#remove {
    @apply font-bold;
    @apply absolute top-1 right-1;
    @apply w-6 h-6;
    @apply flex justify-center items-center;
    @apply bg-white/50 hover:bg-white transition-colors;
    @apply rounded-full border-2 border-gray-500 hover:border-gray-800;
    @apply cursor-pointer;
}
</style>

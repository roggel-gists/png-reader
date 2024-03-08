<template>
    <div
        class="p-4 flex flex-grow-1 gap-4 min-h-screen items-center justify-center text-center"
        @dragover.prevent
        @dragleave.prevent
        @drop.prevent="dropped"
    >
        <ImagePreview :image="image" @reset="image = null" v-if="image" />
        <ImageDetails :image="image" v-if="image" />
    </div>
</template>

<script setup lang="ts">
    useHead({
        bodyAttrs: {
            class: 'bg-gray-900'
        }
    });

    const image = ref<File | null>(null);
    const dropped = (e: { dataTransfer: { files: any; }; }) => {
        image.value = e.dataTransfer.files[0];
    }
</script>
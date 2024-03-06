<script setup lang="ts">
const dragStatus = ref('outside');
const emit = defineEmits(['dropped']);
const dragover = () => {
    dragStatus.value = 'over'
};
const dragleave = () => {
    dragStatus.value = 'outside'
}
const drop = (e: { dataTransfer: { files: any; }; }) => {
    dragStatus.value = 'outside'
    emit('dropped', e.dataTransfer.files[0]);
}

const helperText = computed(() => {
    return dragStatus.value === 'outside'
        ? 'DRAG FILE HERE'
        : 'DROP IT'
});
</script>

<template>
    <div
        class="dropbox"
        :class="{active: dragStatus === 'over'}"
        @dragover.prevent="dragover"
        @dragleave.prevent="dragleave"
        @drop.prevent="drop"
    >
        <h1 v-text="helperText" />
    </div>
</template>

<style scoped>
.dropbox {
    @apply border-4 border-dashed rounded-2xl border-gray-700;
    @apply max-w-[50vw] h-[50vh];
    @apply flex items-center justify-center;
}

.dropbox.active {
    @apply border-gray-600;
}

h1 {
    @apply font-extrabold text-4xl;
    @apply text-gray-700;
    @apply pointer-events-none;
    text-shadow: 0 1px 0 rgba(255,255,255,.2), 0 -1px 0 rgba(0,0,0,0.25);
}
</style>
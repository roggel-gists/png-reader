<script setup lang="ts">
import ExifReader, {type Tags} from 'exifreader';

const props = defineProps<{
    image: File
}>();
const tags = ref<Tags | []>();
const prompts = ref<String[] | []>();
const positivePrompts = ref<String[] | []>([]);
const negativePrompts = ref<String[] | []>([]);
const promptObject = ref<any>();

const parseTags = async () => {
    positivePrompts.value = [];
    negativePrompts.value = [];

    tags.value = await ExifReader.load(props.image, {includeUnknown: true});
    // ComfyUI
    if (tags.value.prompt) {
        promptObject.value = JSON.parse(tags.value.prompt.value);
        positivePrompts.value = findPositives('positive');
        negativePrompts.value = findPositives('negative');
    }
};

const findPositives = (type: string) => {
    const parents = Object.values(promptObject.value).filter((node: any) => {
        return !!node.inputs[type];
    });

    return parents.map((parent: any) => {
        const childId = parent.inputs[type][0];
        if (childId && Object.keys(promptObject.value).includes(childId)) {
            return promptObject.value[childId].inputs.text;
        }
    }).filter(val => !!val);
}

watch(
    () => props.image,
    () => {
        parseTags()
    })
</script>

<template>
    <div class="text-left">
        <p class="text-gray-400" v-if="image" v-text="image.name"/>
        <p class="text-gray-400" v-if="image" v-text="image.type"/>
        <p class="text-gray-400" v-if="image" v-text="Math.round(image.size / 1000) + ' kb'"/>
        <hr class="mb-4">
        <p
            v-for="(prompt, index) in positivePrompts"
            :key="index" v-text="prompt"
            class="bg-green-500/10 p-4 border border-green-950 rounded-lg text-gray-300 mb-4"
        />
        <p
            v-for="(prompt, index) in negativePrompts"
            :key="index" v-text="prompt"
            class="bg-red-500/10 p-4 border border-red-950 rounded-lg text-gray-300 mb-4"
        />
    </div>
</template>

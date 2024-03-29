<script setup lang="ts">
import ExifReader, {type Tags} from 'exifreader';

const props = defineProps<{
    image: File
}>();
const tags = ref<Tags | []>();
const positivePrompts = ref<String[] | []>([]);
const negativePrompts = ref<String[] | []>([]);
const details = reactive({});
const promptObject = ref<any>();
const application = ref<string>();

const parseTags = async () => {
    positivePrompts.value = [];
    negativePrompts.value = [];
    Object.keys(details).forEach(key => delete details[key]);
    promptObject.value = {};

    tags.value = await ExifReader.load(props.image, {includeUnknown: true});
    if (tags.value.prompt) {
        // ComfyUI
        application.value = 'ComfyUI';
        promptObject.value = JSON.parse(tags.value.prompt.value);
        positivePrompts.value = findPositives('positive');
        negativePrompts.value = findPositives('negative');
    } else if (tags.value.parameters) {
        // Automatic 1111
        application.value = 'Automatic 1111';
        const params = tags.value.parameters.value;
        const [prompts, rest] = params.split('Steps: ', 2);
        const [positivePrompt, negativePrompt] = prompts.split('Negative prompt: ', 2);
        positivePrompts.value = [positivePrompt];
        negativePrompts.value = [negativePrompt];
        ('Steps: ' + rest).split(', ').forEach((pair) => {
            const [key, value] = pair.split(': ');
            details[key] = value.trim();
        })
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

const addToClipboard = async (text: string) => {
    await navigator.clipboard.writeText(text);
};

onMounted(() => {
    parseTags();
});

watch(
    () => props.image,
    () => {
        parseTags()
    })
</script>

<template>
    <div class="text-left">
        <h2 class="text-gray-400 text-4xl font-bold mb-4" v-text="application" />
        <p class="text-gray-400 font-bold mb-2" v-text="image.name"/>
        <p class="text-gray-400 mb-2">
            {{ Math.round(image.size / 1000) }}kb
            ({{ tags?.['Image Width'].value }} x {{ tags?.['Image Height'].value }} px)
        </p>
        <hr class="mb-4">
        <p
            v-for="(prompt, index) in positivePrompts"
            :key="index"
            v-text="prompt"
            @dblclick="addToClipboard(prompt.trim())"
            class="bg-green-500/10 p-4 border border-green-950 rounded-lg text-gray-300 mb-4 whitespace-pre-line"
        />
        <p
            v-for="(prompt, index) in negativePrompts"
            :key="index"
            v-if="negativePrompts"
            v-text="prompt"
            @dblclick="addToClipboard(prompt.trim())"
            class="bg-red-500/10 p-4 border border-red-950 rounded-lg text-gray-300 mb-4 whitespace-pre-line"
        />
        <div v-if="details" class="bg-gray-700 p-4 border border-gray-900 rounded-lg text-gray-300 mb-4">
            <table>
                <tr v-for="(value, key) in details" :key="key">
                    <td>{{ key }}</td>
                    <td>{{ value }}</td>
                </tr>
            </table>
        </div>
    </div>
</template>

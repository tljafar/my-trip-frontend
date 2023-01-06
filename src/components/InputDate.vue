<template>
    <div>
        <label class="block">
            <span class="text-gray-700">{{ label }}</span>
            <input
                type="date"
                :min="today"
                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
                :placeholder="placeholder"
                @input="$emit('input', $event.target.value)">
        </label>
        <InvalidFeedback :error="error" />
    </div>
</template>

<script setup>
import InvalidFeedback from './InvalidFeedback.vue';

const props = defineProps({
    label: {
        type: String,
        default: ''
    },
    placeholder: {
        type: String,
        default: ''
    },
    error: {
        type: String,
        default: ''
    }
});
const emit = defineEmits(['input']);

const date = new Date()
const today = date.getFullYear() + '-' + (date.getMonth() + 1).toString().padStart(2, '0') + '-' + date.getDate().toString().padStart(2, '0')
</script>

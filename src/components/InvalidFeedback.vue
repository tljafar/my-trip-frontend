<template>
    <span v-if="error" class="text-red-500 my-2 block text-sm">{{ error }}</span>
</template>

<script setup>
defineProps({
    error: {
        type: String,
        default: ''
    }
})
</script>

<template>
    <div class="relative">
        <label class="block">
            <span class="text-gray-700">{{ label }}</span>
            <input
                ref="inputSearchRef"
                type="search"
                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
                :placeholder="placeholder"
                @input="onInput($event.target.value)">
        </label>
        <InvalidFeedback :error="error" />
        <ul v-if="isResultOpen" class="bg-white rounded-md overflow-y-auto border max-h-[150px] absolute left-0 right-0 z-10">
            <li v-for="(item, index) in results" :key="index" class="px-3 py-2 hover:bg-blue-100 transition cursor-pointer border-b flex justify-between items-center" @click="onItemSelect(item)">
                <div>
                    <div>{{ item.city.name }}, {{ item.country.name }}</div>
                    <div class="text-gray-400 text-sm">{{ item.name }}</div>
                </div>
                <div class="text-gray-400 text-sm border border-gray-400 py-0.5 px-1">{{ item.iata }}</div>
            </li>
        </ul>
    </div>
</template>

<script setup>
import { ref } from 'vue';
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

const emit = defineEmits(['search']);

const isResultOpen = ref(false);
const results = ref([]);
const itemSelected = ref({});
const inputSearchRef = ref(null)


let isTimeout = null;
async function onInput(search) {
    if (isTimeout) clearTimeout(isTimeout);
    isTimeout = setTimeout(async () => {
        const searchedValue = search.trim()
        isResultOpen.value = searchedValue.length >= 3;
        results.value = [];
        if (searchedValue.length >= 3) {
            const response = await fetch(`https://dev.charterpad.com/serve/api/airport/${searchedValue}/keyword`);
            const response_results = await response.json();
            results.value = await response_results.result
            isResultOpen.value = await response_results.result.length > 0
        }else{
            emit('search', {})
        }
    }, 250);

}
function onItemSelect(item) {
    itemSelected.value = item;
    inputSearchRef.value.value = itemSelected.value.fullName;
    isResultOpen.value = false;
    emit('search', itemSelected.value);
}

</script>

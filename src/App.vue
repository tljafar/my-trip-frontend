<template>
  <div class="container max-w-7xl mx-auto px-6 sm:px-12 my-12">
    <div class="my-6 text-center">
      <h1 class="text-5xl font-bold">My Trip</h1>
      <p class="text-gray-400 mt-2">Book your flight now.</p>
    </div>
    <div class="grid md:grid-cols-4 gap-6 border rounded-md shadow-md p-6">
      <AutoCompleteSearch label="From" placeholder="Enter from" :error="errors.from" @search="formData.from = $event.id" />
      <AutoCompleteSearch label="To" placeholder="Enter to" :error="errors.to" @search="formData.to = $event.id" />
      <InputDate label="Departure Date" :error="errors.departure_date" @input="formData.departure_date = $event" />
      <div class="mt-6">
        <button
          type="submit"
          class="mt-1 w-full inline-flex justify-center rounded-md border border-transparent bg-indigo-600 py-2 px-4 text-sm font-medium text-white shadow-sm hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 leading-6"
          @click="onSearch">SEARCH</button>
      </div>
    </div>
    <div v-if="trips.length > 0" class="mt-12">
      <div class="font-semibold text-3xl">Flights</div>
      <div
        v-for="(trip, index) in trips" :key="index"
        class="border rounded-md shadow-md p-6 mt-6 flex justify-between">
        <div>{{ trip.title }}</div>
        <button type="button" class="inline-flex justify-center rounded-md border border-transparent bg-indigo-600 py-2 px-4 text-sm font-medium text-white shadow-sm hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 leading-6">Book</button>
      </div>
    </div>
    <div v-if="noTripsFound" class="text-2xl my-6 text-gray-500">Sorry, Flights Not Found</div>
  </div>
</template>
<script setup>
import { ref, watchEffect } from 'vue';
import AutoCompleteSearch from './components/AutoCompleteSearch.vue';
import InputDate from './components/InputDate.vue';

const formData = ref({
  from: '',
  to: '',
  departure_date: ''
});

const errors = ref({});
const trips = ref([]);
const noTripsFound = ref(false);

const isLoading = ref(false);
let isSubmitting = null;

watchEffect(() => {
  const { from, to, departure_date } = formData.value;
  if (!(from && to && departure_date)) {
    trips.value = [];
  }
})

async function onSearch() {
  isLoading.value = true;
  if (isSubmitting) clearTimeout(isSubmitting);
  isSubmitting = setTimeout(async () => {
    errors.value = {};
    const { from, to, departure_date } = formData.value;
    if (!from) {
      errors.value.from = 'Enter departure';
    }
    if (!to) {
      errors.value.to = 'Enter destination';
    }
    if (!departure_date) {
      errors.value.departure_date = 'Enter departure date';
    }
    const isNotError = Object.values(errors.value).filter(item => item).length === 0;

    if (isNotError) {
      try {
        const response = await fetch(`https://dev.charterpad.com/serve/api/trip/search?departureAirportId${from}&arrivalAirport&etd=${departure_date}&pageSize=20&pageNumber=1`)
        const response_results = await response.json();
        trips.value = await response_results.result.trips;
        noTripsFound.value = trips.value.length === 0
      } catch (e) {
        console.log(e);
      }
    }
    isLoading.value = false;
  }, 250);

}
</script>

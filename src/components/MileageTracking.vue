<template>
  <div class="flex h-screen w-full flex-col text-center self-center">
    <p>current latitude: {{ currentLatitude }}</p>
    <p>current longitude: {{ currentLongtitude }}</p>

    <label class="relative inline-flex items-center me-5 cursor-pointer">
      <input
        type="checkbox"
        value=""
        class="sr-only peer"
        v-model="isTracking"
        @change="startTracking"
      />
      <div
        class="w-11 h-6 bg-gray-200 rounded-full peer dark:bg-gray-700 peer-focus:ring-4 peer-focus:ring-purple-300 dark:peer-focus:ring-purple-800 peer-checked:after:translate-x-full rtl:peer-checked:after:-translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-0.5 after:start-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all dark:border-gray-600 peer-checked:bg-purple-600"
      ></div>
      <span class="ms-3 text-sm font-medium text-gray-900 dark:text-gray-300"
        >Start tracking</span
      >
    </label>
    <p>updated Latitude {{ updatedLatitude }}</p>
    <p>updated longitude {{ updatedLongitude }}</p>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';

const updatedLatitude = ref(0);
const updatedLongitude = ref(0);
const isTracking = ref(false);
const currentLatitude = ref(0);
const currentLongtitude = ref(0);
const hasAllowedPosition = ref(false);

const currentPosition = () => {
  if (hasAllowedPosition) {
    // Function to handle successful position retrieval
    function successCallback(position: any) {
      const latitude = position.coords.latitude;
      const longitude = position.coords.longitude;
      const accuracy = position.coords.accuracy / 1000;

      console.log(position);
      console.log(
        `Current Position: Latitude ${latitude}, Longitude ${longitude}, accuracy ${accuracy}`
      );

      currentLatitude.value = latitude;
      currentLongtitude.value = longitude;
    }

    // Function to handle errors during position retrieval
    function errorCallback(error: any) {
      console.error(`Error getting current position: ${error.message}`);
    }

    // Options for geolocation
    const geolocationOptions = {
      enableHighAccuracy: true,
      timeout: 5000,
      maximumAge: 0,
    };

    // Call getCurrentPosition with the success and error callbacks
    navigator.geolocation.getCurrentPosition(
      successCallback,
      errorCallback,
      geolocationOptions
    );
  }
};

currentPosition();

const startTracking = () => {
  if (navigator.geolocation && isTracking) {
    hasAllowedPosition.value = true;
    navigator.geolocation.watchPosition(
      (position) => {
        updatedLatitude.value = position.coords.latitude;
        updatedLongitude.value = position.coords.longitude;
      },
      (error) => {
        console.error(error.message);
      },
      { enableHighAccuracy: true, timeout: 5000, maximumAge: 0 }
    );
  } else {
    hasAllowedPosition.value = false;
    alert('Geolocation is not supported by this browser.');
  }
};
</script>

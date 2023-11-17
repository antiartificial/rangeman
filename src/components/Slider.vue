<script setup>
import {computed, reactive, toRefs, watch} from 'vue'
import VueSlider from 'vue-slider-component'
import 'vue-slider-component/theme/antd.css'

// Sample records
const sampleRecords = [
  { productID: 1, storeID: 9100, calculate_from_ts: 1 },
  { productID: 2, storeID: 9100, calculate_from_ts: 2 },
  { productID: 3, storeID: 9100, calculate_from_ts: 3 },
  { productID: 4, storeID: 9100, calculate_from_ts: 4 },
  { productID: 5, storeID: 9100, calculate_from_ts: 5 },
  { productID: 6, storeID: 9100, calculate_from_ts: 6 },
  { productID: 7, storeID: 9100, calculate_from_ts: 7 },
  { productID: 8, storeID: 9100, calculate_from_ts: 8 },
  { productID: 9, storeID: 9100, calculate_from_ts: 9 },
  { productID: 10, storeID: 9100, calculate_from_ts: 10 },
  { productID: 11, storeID: 9100, calculate_from_ts: 11 },
  { productID: 12, storeID: 9100, calculate_from_ts: 12 },
  { productID: 13, storeID: 9100, calculate_from_ts: 13 },
  { productID: 14, storeID: 9100, calculate_from_ts: 14 },
  { productID: 15, storeID: 9100, calculate_from_ts: 15 },
  { productID: 16, storeID: 9100, calculate_from_ts: 16 },
  { productID: 17, storeID: 9100, calculate_from_ts: 17 },
  { productID: 18, storeID: 9100, calculate_from_ts: 18 },
  { productID: 19, storeID: 9100, calculate_from_ts: 19 },
];

const state = reactive({
  value: [12, 13], // Main slider values
  overrideValue: 0, // Override value from text field
  overrideRange: [12, 12], // Initialize with windowEnd as both start and end
  records: sampleRecords,
  overrideEnabled: false,
  isFixed: true,
  showOverrideSlider: true
});

// Update overrideRange when overrideValue or windowEnd changes
watch([() => state.overrideValue, () => state.value[0]], () => {
  const start = state.value[0] - state.overrideValue;
  state.overrideRange = [Math.max(start, 0), state.value[0]];
});

// Computed property to filter records
const filteredRecords = computed(() => {
  return state.records.filter(record =>
      record.calculate_from_ts >= state.value[0] && record.calculate_from_ts <= state.value[1]
  );
});

// Computed property to filter records based on override value
const overrideFilteredRecords = computed(() => {
  if (state.overrideEnabled) {
    return state.records.filter(record =>
        record.calculate_from_ts >= (state.value[0] - state.overrideValue) && record.calculate_from_ts < state.value[0]
    );
  }
  return [];
});

const { value, isFixed, overrideEnabled, overrideValue, overrideRange, showOverrideSlider } = toRefs(state);
</script>

<template>
  <div class="justify-items-start">
    <div>
      <input type="checkbox" v-model="overrideEnabled" id="override-checkbox">
      <label for="override-checkbox">Enable Override</label>
      <input type="number" v-model="overrideValue" :disabled="!overrideEnabled">
    </div>
    <div>
      <input type="checkbox" v-model="isFixed" id="isFixedCheckbox">
      <label for="isFixedCheckbox">Fixed time window</label>
    </div>
    <div>
      <input type="checkbox" v-model="showOverrideSlider" id="show-override-slider-checkbox">
      <label for="show-override-slider-checkbox">Show Override Slider</label>
    </div>
    <div class="py-2">
      <div v-if="showOverrideSlider">
      <vue-slider v-model="overrideRange"
                  :min-range="1"
                  :max-range="24"
                  :min="1"
                  :max="24"
                  :enable-cross="false"
                  class="vue-slider2"
                  :disabled="true"
      >
        <template v-slot:step="{ active, overrideRange }">
          <div :class="['vue-slider-mark-label', 'custom-label', { active }]">{{ overrideRange }}</div>
        </template>
      </vue-slider>
        </div>
      <vue-slider v-model="value"
                  :min-range="1"
                  :max-range="24"
                  :fixed=isFixed
                  :min="1"
                  :max="24"
                  :enable-cross="false"
                  :marks="true"
                  class="vue-slider"
      >
        <template v-slot:step="{ active, value }">
          <div :class="['vue-slider-mark-label', 'custom-label', { active }]">{{ value }}</div>
        </template>
      </vue-slider>
    </div>
    <div v-for="record in filteredRecords" :key="record.productID" class="pt-2">
      <!-- Display each record -->
      <p class="py text-red">Product ID: {{ record.productID }}, Store ID: {{ record.storeID }}, Timestamp: {{ record.calculate_from_ts }}</p>
    </div>
    <div v-for="record in overrideFilteredRecords" :key="record.productID" class="override-record">
      <p>Override Record: Product ID: {{ record.productID }}, Store ID: {{ record.storeID }}, Timestamp: {{ record.calculate_from_ts }}</p>
    </div>
  </div>
</template>

<style>

.vue-slider2 .vue-slider-process {
  background: red;
}
.override-record p {
  color: red;
}
.vue-slider {
  padding-bottom: 40px;
}
</style>
<script setup lang="ts">
import MultiSelect from 'primevue/multiselect';
import Button from "primevue/button";
import { ref, computed } from 'vue';

const selectedCities = ref<any[]>([]);
const cities = ref([
  { name: 'Saratov' },
  { name: 'Moscow' },
  { name: 'SPB' }
]);

const showClearButton = computed(() => selectedCities.value.length > 0);

const clearSelection = () => {
  selectedCities.value = []
}
</script>

<template>
  <div class="multiselect-wrapper">
    <MultiSelect
        v-model="selectedCities"
        :options="cities"
        optionLabel="name"
        placeholder="Выберите города"
        class="multiselect"
    />
    <div class="clear-button">
      <Button v-if="showClearButton"
              @click="clearSelection"
              icon="pi pi-times"
              severity="danger"
              text
              rounded
              aria-label="Cancel" />
    </div>
  </div>
</template>

<style scoped lang="scss">
.multiselect-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.clear-button {
  position: absolute;
  right: 25px;
  top: 50%;
  transform: translateY(-50%);
}

.multiselect {
  width: 100%;
}
</style>

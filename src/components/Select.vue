<script setup lang="ts">
import MultiSelect from 'primevue/multiselect';
import Button from "primevue/button";
import { ref, computed, onMounted } from 'vue';

interface City {
  name: string;
}

const cities = ref<City[]>([]);
const selectedCities = ref<any[]>([]);

onMounted(async () => {
  try {
    const response = await fetch(`${process.env.BASE_URL}dataset.json`)
    const data = await response.json()
    cities.value = data.cities.map((city: string) => ({ name: city }))
  } catch (error) {
    console.error('Ошибка при загрузке данных:', error)
  }
});

// Регулирует отображение кнопки "Стереть" в поле поиска
const isClearButtonVisible = computed(() => {
  return selectedCities.value.length > 0
})

// clearSelection стирает поле поиска
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
        :showToggleAll="false"
        class="multiselect"
    />
    <div class="clear-button">
      <Button v-if="isClearButtonVisible"
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
  width: 400px;
}

.clear-button {
  position: absolute;
  right: 25px;
  top: 50%;
  transform: translateY(-50%);
}

.multiselect {
  width: 100%;
  font-family: sans-serif;
}
</style>

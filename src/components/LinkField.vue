<template>
  <div class="component-wrapper">
    <InputText
        v-if="isEditing || !url"
        v-model="url"
        :placeholder="placeholder"
        @blur="handleBlur"
        @keypress.enter="handleBlur"
    />
    <div v-else class="link-display">
      <a :href="url">{{ title }}</a>
      <i class="pi pi-pencil" @click="handleEditClick"></i>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import InputText from "primevue/inputtext";

const isEditing = ref(true);
const url = ref("");
const title = ref();
const placeholder = "https://";

const fetchTitle = async (url: string) => {
  try {
    const response = await fetch('https://cors-anywhere.herokuapp.com/' + url);
    const text = await response.text();
    const matches = text.match(/<title>([^<]*)<\/title>/);
    title.value = matches ? matches[1] : 'Заголовок не найден';
  } catch (error) {
    console.error('Ошибка при получении заголовка:', error);
    title.value = 'Не удалось получить заголовок';
  }
};

const handleBlur = async () => {
  if (url.value) {
    title.value = await fetchTitle(url.value);
    isEditing.value = false;
  }
}

const handleEditClick = () => {
  isEditing.value = true;
}
</script>

<style>
.link-display {
  display: flex;
  align-items: center;
}

.pi-pencil {
  cursor: pointer;
  margin-left: 8px;
}
</style>

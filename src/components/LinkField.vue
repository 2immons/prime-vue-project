<script setup lang="ts">
import { ref } from "vue";
import InputText from "primevue/inputtext";

const isEditing = ref(true)
const url = ref("")
const title = ref("")
const placeholder = "https://"

const fetchTitle = async (url: string): Promise<string> => {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error('Получен некорректный ответ от сервера')
    }
    const htmlText = await response.text()
    const parser = new DOMParser()
    const doc = parser.parseFromString(htmlText, 'text/html')
    return doc.querySelector('title')?.innerText || 'Заголовок не найден'
  } catch (error) {
    console.error('Ошибка при получении заголовка:', error)
    return 'Не удалось получить заголовок (возможно, CORS)'
  }
};

const handleBlur = async () => {
  if (url.value) {
    title.value = await fetchTitle(url.value)
    isEditing.value = false;
  }
}

const handleEditClick = () => {
  isEditing.value = true
}
</script>

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
      <a :href="url" target="_blank">{{ title }}</a>
      <i class="pi pi-pencil" @click="handleEditClick"></i>
    </div>
  </div>
</template>

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

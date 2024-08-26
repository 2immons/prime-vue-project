<script setup lang="ts">
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import { onMounted, ref } from 'vue';

const rawString = ref<string[]>([])
const logMessages = ref<{ messages: string[] }[]>([])

onMounted(async () => {
  try {
    const response = await fetch('/dataset.json')
    const data = await response.json()
    rawString.value = data.raw_strings

    logMessages.value = rawString.value.map(log => ({
      messages: parseLogString(log)
    }))
  } catch (error) {
    console.error('Ошибка при загрузке данных:', error)
  }
})

// parseLogString возвращает массив из строки, разделенной по меткам времени
const parseLogString = (logString: string): string[] => {
  return logString.split(/(?=\[\d{2}:\d{2}:\d{2}])/)
      .map(msg => msg.trim())
      .filter(msg => msg.length > 0);
}
</script>

<template>
  <div class="component-wrapper">
    <DataTable :value="logMessages" tableStyle="min-width: 50rem">
      <Column header="Сообщение">
        <template #body="{ data }">
          <ul class="log-list">
            <li v-for="(msg, index) in data.messages" :key="index">{{ msg }}</li>
          </ul>
        </template>
      </Column>
    </DataTable>
  </div>
</template>

<style scoped>
.log-list {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.log-list li {
  white-space: pre-wrap;
}
</style>

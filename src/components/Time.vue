<script setup lang="ts">
import Calendar from 'primevue/calendar'
import Button from 'primevue/button'
import Divider from "primevue/divider";
import { ref, computed } from 'vue'

const date = ref<Date | null>(null)

const setCurrentTime = () => {
  const now = new Date()
  now.setSeconds(0)
  date.value = now
}

const clearDate = () => {
  date.value = null
}

const formattedTime = computed(() => {
  if (!date.value) return ''

  const hours = date.value.getHours().toString().padStart(2, '0')
  const minutes = date.value.getMinutes().toString().padStart(2, '0')

  return `${hours}:${minutes}`
})
</script>

<template>
  <div class="component-wrapper">
    <label for="calendar">Значение переменной date: {{ formattedTime }}</label>
    <Calendar id="calendar" v-model="date" timeOnly class="calendar">
      <template #footer>
        <Divider />
        <div class="calendar-footer">
          <Button label="Текущее время" @click="setCurrentTime" text/>
          <Button label="Очистить" @click="clearDate" text/>
        </div>
      </template>
    </Calendar>
  </div>
</template>

<style scoped lang="scss">
.calendar-footer {
  display: flex;
  justify-content: space-between;
  padding: 0.5rem;
}

.calendar {
  width: 400px;
}
</style>

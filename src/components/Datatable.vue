<script setup lang="ts">
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import { ref } from 'vue';

const rawString = `[13:36:53] Расчетное время: 9 мин[13:36:58] Открыть клапан откачки К1[13:36:58] Включить вакуумный насос[13:36:58] Закрыть клапан К5[13:36:58] Закрыть клапан дистилляции К2[13:36:58] Ожидание: 8 с[13:37:06] Заливка 2.2мл. в испаритель[13:37:06] Заданно 26.50602409638554 шагов[13:37:09] Заливка перекиси завершена[13:37:09] Открыть клапан дистилляции К2[13:42:09] Включить нагрев испарителя[13:42:09] Закрыть клапан дистилляции К2[13:42:09] конечное давление1.0960040758227925 торр[13:42:09] Выпаривание через К2[13:42:09] Выпаривание длилось5 мин[13:42:09] Откачка до 1 торр[13:42:15] Закрыть клапан откачки К1[13:43:09] Открыть клапан откачки К1[13:43:14] Аппаратное смещение 0 денсит. = -0.313683180809021[13:43:14] Закрыть клапан дистилляции К2`;

const parseLogString = (logString: string) => {
  return logString.split(/(?=\[\d{2}:\d{2}:\d{2}\])/)
                  .map(msg => msg.trim())
                  .filter(msg => msg.length > 0);
};

const logMessages = ref(parseLogString(rawString));
</script>

<template>
  <div class="component-wrapper">
    <DataTable :value="[{}]" tableStyle="min-width: 50rem">
      <Column header="Номер"></Column>
      <Column header="Сообщение">
        <template #body="{ data }">
          <ul class="log-list">
            <li v-for="(message, index) in logMessages" :key="index">{{ message }}</li>
          </ul>
        </template>
      </Column>
    </DataTable>
  </div>
</template>

<style scoped lang="scss">
.log-list {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.log-list li {
  white-space: pre-wrap
}
</style>
<script setup lang="ts">
import InputText from 'primevue/inputtext'
import InputGroup from 'primevue/inputgroup';
import InputGroupAddon from 'primevue/inputgroupaddon';
import Button from "primevue/button";
import DataTable from 'primevue/datatable';
import Checkbox from "primevue/checkbox";
import Column from 'primevue/column';
import { ref } from 'vue';
import MultiSelect from "primevue/multiselect";
import Rating from "primevue/rating";
import Tag from "primevue/tag";

const searchText = ref()

const getSeverity = (data: any) => {
  switch (data.status) {
    case 'INSTOCK':
      return 'success'
    case 'LOWSTOCK':
      return 'warning'
    case 'OUTOFSTOCK':
      return 'danger'
    default:
      return null
  }
}


const isFieldsMenuVisible = ref(false)
const toggleFieldsMenu = () => {
  isFieldsMenuVisible.value = !isFieldsMenuVisible.value
}

const formatCurrency = (amount: number) => {
  return `$${amount.toFixed(2)}`
}

const selectedCities = ref<any[]>([]);
const cities = ref([
  { code: "f230fhD3g", checked: false, name: 'Saratov', status: "INSTOCK", reviews: 3, price: 100, category: "Категория 1" },
  { code: "f2ffhD3g", checked: false, name: 'Moscow', status: "LOWSTOCK", reviews: 4, price: 100, category: "Категория 1" },
  { code: "f255gfhD3g", checked: false, name: 'SPB', status: "OUTOFSTOCK", reviews: 5, price: 150, category: "Категория 2" }
]);

const fields = ref([
  { name: 'code', displayName: 'Код' },
  { name: 'name', displayName: 'Наименование' },
  { name: 'image', displayName: 'Картинка' },
  { name: 'price', displayName: 'Цена' },
  { name: 'category', displayName: 'Категория' },
  { name: 'reviews', displayName: 'Рейтинг' },
  { name: 'status', displayName: 'Статус' }
]);

const selectedFields = ref(fields);

const searchFields = ref([
  { name: 'code', displayName: 'Код' },
  { name: 'name', displayName: 'Наименование' },
  { name: 'price', displayName: 'Цена' },
  { name: 'category', displayName: 'Категория' },
  { name: 'reviews', displayName: 'Рейтинг' },
  { name: 'status', displayName: 'Статус' }
]);

const selectedSearchFields = ref(fields);
</script>

<template>
  <div class="component-wrapper">
    <DataTable
        :value="cities"
        paginator :rows="5" paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
        currentPageReportTemplate="Showing {first} to {last} of {totalRecords} products"
        :rowsPerPageOptions="[1, 3, 5, 10, 20, 50]"
    >
      <template #header>
        <div class="header">
          <InputGroup class="search-field">
            <MultiSelect
                v-model="selectedSearchFields"
                :options="searchFields"
                optionLabel="displayName"
            />
            <InputText placeholder="Поиск..." v-model="searchText"/>
            <Button label="Search" />
          </InputGroup>
          <MultiSelect class="datatable-filter"
              v-model="selectedFields"
              :options="fields"
              optionLabel="displayName"
              placeholder="Поля таблицы"
          />
        </div>
      </template>
      <Column field="price" header="" style="width: 3%">
        <template #body="slotProps">
          <Checkbox v-model="slotProps.data.checked" :binary="true" />
        </template>
      </Column>
      <Column field="code" header="Code" sortable style="width: 15%"></Column>
      <Column field="name" header="Name" sortable style="width: 20%"></Column>
      <Column header="Image" style="width: 10%">
        <template #body="slotProps">
          <img :src="`https://primefaces.org/cdn/primevue/images/product/${slotProps.data.image}`" :alt="slotProps.data.image" class="w-24 rounded" />
        </template>
      </Column>
      <Column field="price" header="Price" sortable style="width: 11%">
        <template #body="slotProps">
          {{ formatCurrency(slotProps.data.price) }}
        </template>
      </Column>
      <Column field="category" header="Category" sortable style="width: 15%"></Column>
      <Column field="reviews" header="Reviews" style="width: 14%" sortable>
        <template #body="slotProps">
          <Rating :modelValue="slotProps.data.reviews" readonly :cancel="false" />
        </template>
      </Column>
      <Column header="Status" style="width: 12%" sortable>
        <template #body="slotProps">
          <Tag :value="slotProps.data.status" :severity="getSeverity(slotProps.data)" />
        </template>
      </Column>
    </DataTable>
  </div>
</template>

<style scoped lang="scss">
.header {
  display: flex;
  flex-direction: row;
  gap: 10px;
}

.search-field {
  width: 80%;
}

.datatable-filter {
  width: 20%;
}

.log-list {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.log-list li {
  white-space: pre-wrap
}
</style>
<script setup lang="ts">
import InputText from 'primevue/inputtext'
import InputGroup from 'primevue/inputgroup';
import Button from "primevue/button";
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import {ref, onMounted, watch, computed} from 'vue';
import MultiSelect from "primevue/multiselect";
import Rating from "primevue/rating";
import Tag from "primevue/tag";

interface Product {
  code: string
  checked: boolean
  name: string
  status: string
  reviews: number
  price: number
  category: string
  image: string
}

interface Field {
  name: keyof Product
  displayName: string
}

const products = ref<Product[]>([]) // продукты

const filteredProducts = ref<Product[]>([]) // фильтрованные поиском продукты
const selectedProducts = ref() // выбранные в таблице продукты

// Получение продуктов из dataset
onMounted(async () => {
  try {
    const response = await fetch(`${process.env.BASE_URL}dataset.json`)
    const data = await response.json()
    products.value = data.products.map((product: Product) => ({
      code: product.code,
      name: product.name,
      status: product.status,
      reviews: product.reviews,
      price: product.price,
      category: product.category,
      image: product.image
    }))
    filteredProducts.value = products.value;
  } catch (error) {
    console.error('Ошибка при загрузке данных:', error)
  }
})

// getSeverity возвращает тип тэга в зависимости от статуса
const getSeverity = (status: string) => {
  switch (status) {
    case 'INSTOCK':
      return 'success'
    case 'LOWSTOCK':
      return 'warn'
    case 'OUTOFSTOCK':
      return 'danger'
    default:
      return null
  }
}

// formatCurrency форматирует валюту
const formatCurrency = (amount: number) => {
  return `$${amount.toFixed(2)}`
}

// getWidthForField задает ширину колонки по её наименованию
const getWidthForField = (fieldName: string) => {
  switch (fieldName) {
    case 'code':
      return '15%'
    case 'name':
      return '20%'
    case 'price':
      return '11%'
    case 'category':
      return '15%'
    case 'reviews':
      return '14%'
    case 'status':
      return '12%'
    default:
      return 'auto'
  }
}

const columns = ref([
  { name: 'code', displayName: 'Код', index: 0 },
  { name: 'name', displayName: 'Наименование', index: 1 },
  { name: 'image', displayName: 'Картинка', index: 2 },
  { name: 'price', displayName: 'Цена', index: 3 },
  { name: 'category', displayName: 'Категория', index: 4 },
  { name: 'reviews', displayName: 'Рейтинг', index: 5 },
  { name: 'status', displayName: 'Статус', index: 6 },
])

const selectedColumns = ref([...columns.value])

// Сортировка отображаемых колонок для поддержания первоначального порядка после их включения/отключения
watch(selectedColumns, () => {
  selectedColumns.value.sort((a, b) => a.index - b.index)
})

const searchFields = ref([
  { name: 'code', displayName: 'Код' },
  { name: 'name', displayName: 'Наименование' },
  { name: 'price', displayName: 'Цена' },
  { name: 'category', displayName: 'Категория' },
  { name: 'reviews', displayName: 'Рейтинг' },
  { name: 'status', displayName: 'Статус' }
])

const selectedSearchFields = ref([])

// Поисковой запрос
const searchText = ref()

// applySearchFilter выполняет фильтрацию согласно поисковому запросу и выбранным полям для поиска
const applySearchFilter = () => {
  if (!searchText.value) {
    filteredProducts.value = products.value
    return
  }

  const lowerCaseSearchText = searchText.value.toLowerCase()

  filteredProducts.value = products.value.filter((product: Product) => {
    return selectedSearchFields.value.some((field: Field) => {
      const fieldValue = product[field.name].toString().toLowerCase()
      return fieldValue.includes(lowerCaseSearchText)
    })
  })
}

// handleKeyPress проверяет нажатие Enter и вызывает фильтрацию
const handleKeyPress = (event: KeyboardEvent) => {
  if (event.key === 'Enter') {
    applySearchFilter()
  }
}

// Регулирует отображение кнопки "Стереть" в поле поиска
const isClearButtonVisible = computed(() => {
  if (searchText.value === undefined) return false
  else {
    if (searchText.value.length > 0) return true
  }
})

// clearSelection стирает поле поиска
const clearSelection = () => {
  searchText.value = ''
}
</script>

<template>
  <div class="component-wrapper">
    <DataTable
        :value="filteredProducts"
        v-model:selection="selectedProducts"
        paginator
        :rows="3"
        paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
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
                placeholder="Поля поиска"
            />
            <div class="search-input-wrapper">
              <InputText placeholder="Поиск..." v-model="searchText" class="search-input" @keypress="handleKeyPress"/>
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
            <Button label="Поиск" @click="applySearchFilter" />
          </InputGroup>
          <MultiSelect
              class="datatable-filter"
              v-model="selectedColumns"
              :options="columns"
              optionLabel="displayName"
              placeholder="Поля таблицы"
          />
        </div>
      </template>

      <Column selectionMode="multiple"></Column>

      <template v-for="field in selectedColumns" :key="field.name">
        <Column v-if="field.name === 'image'" header="Image" style="width: 10%">
          <template #body="slotProps">
            <img :src="`https://primefaces.org/cdn/primevue/images/product/${slotProps.data.image}`" :alt="slotProps.data.image" class="w-24 rounded" />
          </template>
        </Column>
        <Column v-else :field="field.name" :header="field.displayName" :sortable="true" :style="{ width: getWidthForField(field.name) }">
          <template v-if="field.name === 'price'" #body="slotProps">
            {{ formatCurrency(slotProps.data.price) }}
          </template>
          <template v-else-if="field.name === 'reviews'" #body="slotProps">
            <Rating :modelValue="slotProps.data.reviews" readonly :cancel="false" />
          </template>
          <template v-else-if="field.name === 'status'" #body="slotProps">
            <Tag :value="slotProps.data.status" :severity="getSeverity(slotProps.data.status)" />
          </template>
        </Column>
      </template>
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
  display: flex;
}

.datatable-filter {
  width: 20%;
}

.search-input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  width: 80%;
}

.clear-button {
  position: absolute;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

.search-input {
  width: 100%;
  font-family: sans-serif;
}
</style>
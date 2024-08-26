<template>
  <div class="container">
    <LinkField />
    <Time />
    <Select />
    <DataTable />
    <DatatableSearch />
  </div>
</template>

<script setup lang="ts">
import './styles/styles.scss'
import 'primeicons/primeicons.css'

import LinkField from './components/LinkField.vue'
import Time from './components/Time.vue'
import Select from './components/Select.vue'
import DataTable from "./components/Datatable.vue";
import DatatableSearch from "@/components/DatatableSearch.vue";

</script>

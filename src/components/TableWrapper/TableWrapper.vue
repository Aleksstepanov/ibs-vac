<template>
  <q-table :columns="headersConfig" :rows="data" :title="title" class="full-width" separator="cell">
    <template v-slot:body-cell-url="props">
      <q-td :props="props">
        <div>
          <a :href="props.row.url" target="_blank">{{props.row.url}}</a>
        </div>
      </q-td>

    </template>
  </q-table>
</template>
<script setup>
import headersConfig from 'components/TableWrapper/headersConfig'
defineProps({
  data: {
    type: Array,
    default: () => ([])
  },
  title: {
    type: String,
    default: 'Vacancies'
  }
})
</script>

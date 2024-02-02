<template>
  <UiLoader v-if="state.isLoading" />
  <q-page class="flex flex-start align-center justify-center">
    <div class="flex row full-width">
      <SettingsComponent :regions="state.regions" @click:settings="onClickSetting($event)" />
      <TableWrapper v-if="state.vacancies?.length" :data="state.vacancies" />
    </div>
  </q-page>
</template>
<script setup>
import { onMounted, reactive } from 'vue'
import SettingsComponent from 'src/components/Settings'
import axios from 'axios'
import UiLoader from 'components/UiLoader'
import TableWrapper from 'components/TableWrapper/TableWrapper.vue'

// state
const state = reactive({
  isLoading: false,
  regions: [],
  vacancies: []
})

// methods
const onClickSetting = async (payload) => {
  const { target, location, schedule } = payload
  const _target = target?.replace(/ /g, '%')
  console.log(_target)
  const _location = location?.reduce((acc, el) => {
    acc = `${acc}&area=${el?.id}`
    return acc
  }, '') || ''
  const _schedule = schedule?.reduce((acc, el) => {
    acc = `${acc}&schedule=${el?.id}`
    return acc
  }, '')

  state.isLoading = true
  try {
    const res = await axios.get(`${import.meta.env.VITE_HH_API_URL}/vacancies?text=${_target}${_location}${_schedule}`)

    const pages = res?.data?.pages
    if (pages) {
      const promises = []
      for (let i = 0; i < pages; i++) {
        promises.push(axios.get(`${import.meta.env.VITE_HH_API_URL}/vacancies?text=${_target}${_location}${_schedule}&page=${i}`))
      }
      const result = await Promise.all(promises)
      state.vacancies = result?.map(r => r?.data?.items).flat()?.map(v => ({
        city: v?.area?.name || '',
        name: v?.employer?.name || '',
        url: v?.alternate_url || '',
        shedule: v?.employment?.name || '',
        vacancy: v?.name || '',
        // url: v?.url || '',
        id: v?.id || null,
        contacts: v?.contacts || ''
      }))
    } else {
      state.vacancies = res?.data || []
    }
  } catch (e) {
    console.log(e)
  } finally {
    state.isLoading = false
  }
}

// life hooks
onMounted(async () => {
  state.isLoading = true
  try {
    const res = await axios.get(`${import.meta.env.VITE_HH_API_URL}/areas`)
    state.regions = res?.data?.map(region => ({
      id: region?.id || '',
      name: region?.name || ''
    }))
  } catch (e) {
    console.log(e)
  } finally {
    state.isLoading = false
  }
})
</script>

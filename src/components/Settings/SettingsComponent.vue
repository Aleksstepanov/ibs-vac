<template>
  <div class="q-mt-lg full-width">
    <q-expansion-item
      :model-value="state.showSettings"
      expand-separator
      icon="settings"
      label="Settings"
      @update:model-value="state.showSettings = $event"
    >
      <q-card>
        <q-card-section>
          <q-input v-model:model-value="state.target" label="Target Response" />
        </q-card-section>
        <q-card-section>
          <q-select v-model:model-value="state.location"
                    :options="regions"
                    clearable
                    multiple
                    option-label="name"
                    option-value="id"
                    label="Location" />
        </q-card-section>
        <q-card-section>
          <q-select v-model:model-value="state.schedule"
                    :options="schedule"
                    clearable
                    multiple
                    option-label="name"
                    option-value="id"
                    label="Schedule" />

        </q-card-section>
        <q-card-actions>
          <q-btn label="Apply" class="full-width" color="primary" @click="onClickApply" />
        </q-card-actions>
      </q-card>
    </q-expansion-item>
  </div>
</template>
<script setup>
import { reactive } from 'vue'

import schedule from 'src/configs/schedule'

// props
defineProps({
  regions: {
    type: Array,
    default: () => null
  }
})

// emits
const emit = defineEmits(['click:settings'])

// state
const state = reactive({
  target: null,
  location: null,
  schedule: null,
  regions: [],
  showSettings: true
})

// methods
const onClickApply = () => {
  if (state.target) {
    state.showSettings = false
    emit('click:settings', {
      target: state.target,
      location: state.location,
      schedule: state.schedule
    })
  }
}

// lifeHooks

</script>

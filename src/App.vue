<script lang="ts" setup>
import { darkTheme } from 'naive-ui'
import { mainStore } from './store'

const store = mainStore()

const total = ref()
const convertedTotal = ref()

const handleUpdate = (val: any) => {
  total.value = val.value.total
  convertedTotal.value = val.value.convertedTotal
}
</script>

<template>
  <n-config-provider :theme="darkTheme">
    <div class="w-95vw max-w-[750px] mx-auto my-5">
      <n-card title="xChange">
        <div class="flex justify-end">
          <n-h4 class="h-1">
            {{ store.currency }}: {{ total }}
            &nbsp;&nbsp;&nbsp;
            {{ store.convertedCurrency }}: {{ convertedTotal }}
          </n-h4>
        </div>
      </n-card>
      <Suspense>
        <dataTable @updateTotal="handleUpdate" />
        <template #fallback>
          <div class="mt-7 flex justify-center">
            <n-spin />
          </div>
        </template>
      </Suspense>
    </div>
    <n-global-style />
    <ReloadPrompt />
  </n-config-provider>
</template>

<style>
body {
  font-size: 16px;
}

/* Set input inheritance */

input {
  font-size: inherit;
}
</style>
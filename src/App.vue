<script lang="ts" setup>
import { darkTheme } from 'naive-ui'
</script>

<template>
  <n-config-provider :theme="darkTheme">
    <div class="w-85vw max-w-[750px] mx-auto my-5">
      <n-card title="xChange List" />
      <Suspense>
        <dataTable />
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

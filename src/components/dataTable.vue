<script lang="ts" setup>
import { mainStore } from '@/store'
import { NButton } from 'naive-ui'

const store = mainStore()
const pagination = ref()

const emit = defineEmits(['updateTotal'])

// get data from api using fetch
const req = await fetch(`https://api.exchangerate.host/convert?from=${store.currency}&to=${store.convertedCurrency}`)
const res = await req.json()
store.rate = res.info.rate

const openModal = ref(false)
const addName = ref('')
const addAmount = ref()

const openSettings = ref(false)

const tableData = ref([] as any)
store.data.forEach((value, index) => {
  tableData.value.push({
    no: index + 1,
    name: value.Name,
    price: Number(value.Amount).toFixed(2),
    convertedPrice: (Number(value.Amount) * store.rate).toFixed(2)
  })
})
const columns = ref([
  // {
  //   title: 'No',
  //   key: 'no',
  //   width: '10%'
  // },
  {
    title: 'Name',
    key: 'name',
    width: '35%',
    sorter: 'default'
  },
  {
    title: `${store.currency}`,
    key: 'price',
    width: '15%',
    sorter: (a: any, b: any) => a.price - b.price
  },
  {
    title: `${store.convertedCurrency}`,
    key: 'convertedPrice',
    width: '36%',
    sorter: (a: any, b: any) => a.convertedPrice - b.convertedPrice
  },
  {
    title: 'Del',
    key: 'remove',
    width: '9%',
    render(row: any) {
      return h(
        NButton,
        {
          strong: true,
          tertiary: true,
          size: 'small',
          onClick: () => {
            store.data.splice(row.no - 1, 1)
            tableData.value.splice(row.no - 1, 1)
            tableData.value.forEach((value: any, index: any) => {
              value.no = index + 1
            })
          }
        },
        { default: () => 'X' }
      )
    }
  }
])

const addItem = () => {
  store.data.push({
    Name: addName.value,
    Amount: addAmount.value
  })
  tableData.value.push({
    no: tableData.value.length + 1,
    name: addName.value,
    price: Number(addAmount.value).toFixed(2),
    convertedPrice: (Number(addAmount.value) * store.rate).toFixed(2)
  })
  openModal.value = false
}

const summary = computed(() => {
  let total = 0
  let convertedTotal = 0
  tableData.value.forEach((value: any) => {
    total += Number(value.price)
    convertedTotal += Number(value.convertedPrice)
  })
  return {
    total: total.toFixed(2),
    convertedTotal: convertedTotal.toFixed(2)
  }
})

watch(openModal, () => {
  if (!openModal.value) {
    addName.value = ''
    addAmount.value = ''
  }
})

watch([() => store.currency, () => store.convertedCurrency], async () => {
  // get data from api using fetch
  const req = await fetch(`https://api.exchangerate.host/convert?from=${store.currency}&to=${store.convertedCurrency}`)
  const res = await req.json()
  store.rate = res.info.rate
  console.log("YOOOO")
  columns.value[2] = {
    title: `${store.currency}`,
    key: 'price',
    width: '45',
    sorter: (a: any, b: any) => a.price - b.price
  }
  columns.value[3] = {
    title: `${store.convertedCurrency}`,
    key: 'convertedPrice',
    width: '45',
    sorter: (a: any, b: any) => a.convertedPrice - b.convertedPrice
  }
  tableData.value.forEach((value: any) => {
    value.convertedPrice = (Number(value.price) * store.rate).toFixed(2)
  })
})

watchEffect(() => {
  pagination.value = { pageSize: Number(store.pageLimit) }
  if (store.data.length >= 0) {
    emit('updateTotal', summary)
  }
})
</script>

<template>
  <n-data-table class="mt-5" :scroll-x="550" :data="tableData" :columns="columns" :pagination="pagination"
    :max-height="850" />
  <n-modal v-model:show="openModal">
    <div class="flex justify-center mt-30 w-250 max-w-[500px]">
      <n-card title="Add Item">
        <n-form :label-width="80">
          <n-form-item label="Name">
            <n-input v-model:value="addName" />
          </n-form-item>
          <n-form-item label="Price">
            <n-input type="number" v-model:value="addAmount" />
          </n-form-item>
          <n-form-item class="flex justify-end">
            <n-button type="primary" @click="addItem">Add</n-button>
          </n-form-item>
        </n-form>
      </n-card>
    </div>
  </n-modal>
  <n-modal v-model:show="openSettings">
    <div class="flex justify-center mt-30 w-250 max-w-[500px]">
      <n-card title="Settings">
        <n-h3 class="mb-2">Currency</n-h3>
        <n-select v-model:value="store.currency" :options="store.currencies"></n-select>
        <n-h3 class="mb-2">Converted Currency</n-h3>
        <n-select v-model:value="store.convertedCurrency" :options="store.currencies"></n-select>
        <n-h3 class="mb-2">Page Limit</n-h3>
        <n-input type="number" v-model:value="store.pageLimit" />
      </n-card>
    </div>
  </n-modal>
  <n-button class="fixed bottom-5 right-5" @click="openModal = !openModal">
    <i-ph:money-duotone />
  </n-button>
  <n-button class="fixed bottom-5 left-5" @click="openSettings = !openSettings">
    <i-ic:baseline-settings-suggest />
  </n-button>
</template>

<style>
input {
  font-size: 16.2px !important;
}
</style>
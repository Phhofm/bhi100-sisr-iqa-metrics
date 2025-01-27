<script setup lang="ts">
import { onMounted, ref } from 'vue';

interface Column {
  key: string;
  label: string;
  sortable?: boolean;
}

const columns: Ref<Column[]> = ref([]);
const modelMetrics = ref<{ [key: string]: string }[]>([]);

async function fetchData() {

  // fetch data
  const data = await fetch('https://raw.githubusercontent.com/Phhofm/sisr-metrics/refs/heads/main/scores/model_scores.csv')
  const csvdata = await data.text()

  // get column names
  const header = csvdata.split('\n')
  const columnNames = header[0].split(',')
  let id = 0;
  columnNames.forEach(name => {
    name = name.replace(/[\n\r\t]/gm, "");
    columns.value.push({ key: id.toString(), label: name, sortable: true });
    id += 1;
  });

  // get row data
  const rows = csvdata.split('\n').slice(1); // skip header row, extract as array

  rows.forEach(row => {
    const values = row.split(',');
    const metric: { [key: string]: string } = {};
    id = 0;

    columnNames.forEach((name, index) => {
      metric[id.toString()] = values[index];
      id += 1;
    });

    modelMetrics.value.push(metric);
  });
}

onMounted(() => {
  fetchData();
});

const q = ref('')

const filteredRows = computed(() => {
  if (!q.value) {
    return modelMetrics.value
  }

  return modelMetrics.value.filter((model) => {
    return Object.values(model).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase())
    })
  })
})
</script>

<template>
  <UContainer>
    <UCard class="mt-10">
      <template #header>
        <div class="flex justify-between">
          <h1>BHI100 SISR Metrics Table</h1>
          <ColorScheme
            ><USelect
              v-model="$colorMode.preference"
              :options="['system', 'light', 'dark']"
          /></ColorScheme>
        </div>
      </template>
      <div>
        <div class="flex px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
      <UInput v-model="q" placeholder="Filter models ..." />
    </div>
        <UTable
          :columns="columns"
          :rows="filteredRows"
          :loading="modelMetrics.length === 0"
        />
      </div>
      <div class="flex justify-between">
      <UButton
        icon="i-heroicons-book-open"
        to="https://huggingface.co/datasets/Phips/BHI100"
        target="_blank"
        >BHI100 Set</UButton
      >
      <UButton
        icon="i-heroicons-photo"
        to="https://github.com/Phhofm/sisr-metrics/tree/main/inference_results/chaiNNer"
        target="_blank"
        >Inference Results</UButton
      >
      <UButton
        icon="i-heroicons-folder"
        to="https://github.com/Phhofm/sisr-metrics/tree/main/scores"
        target="_blank"
        >Scores</UButton
      >
      </div>
    </UCard>
  </UContainer>
</template>

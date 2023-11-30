<template>
  <div class="title">
    <h3 class="text-blue-600 md:text-center font-bold text-5xl">List of Customers</h3>
  </div>
  <a-table 
  :columns="columns" 
  :row-key="(record: any, index: any) => record.login.uuid" 
  :data-source="data_"
  :pagination="pagination" 
  :loading="loading" 
  @change="handleTableChange">
    <template #bodyCell="{ column, text }">
      <template v-if="column.title"></template>
      <template v-if="column.dataIndex === 'name'">{{ text.first }} {{ text.last }}</template>
      <template v-if="column.dataIndex === 'cell'">{{ text.slice(0, 5) }} ***** {{ text.slice(11, 13) }}</template>
      <template v-if="column.dataIndex === 'dob'">{{ text.age }}</template>
      <template v-if="column.dataIndex === 'registered'">{{ text.age }} Birr</template>
    </template>
  </a-table>
</template>
<script lang="ts" setup>
import { computed } from 'vue';
import type { TableProps } from 'ant-design-vue';
import { usePagination } from 'vue-request';
import axios from 'axios';
const columns = [
  {
    title: 'Customer Name',
    dataIndex: 'name',
    width: '20%',
  },
  {
    title: 'Phone Number',
    dataIndex: 'cell',
    width: '20%',
  },
  {
    title: 'Overdue Days',
    dataIndex: 'dob',
    width: '20%',
  },
  {
    title: 'Outstanding Amount',
    dataIndex: 'registered',
    width: '20%',
  },
];

type APIParams = {
  results: number;
  page?: number;
  sortField?: string;
  sortOrder?: number;
  [key: string]: any;
};
type APIResult = {
  results: {
    gender: 'female' | 'male';
    name: string;
    email: string;
  }[];
};
let data_;
const queryData = async (params: APIParams) => {
  const res = await axios.get<APIResult>('https://randomuser.me/api?noinfo', { params });
  data_ = res?.data?.results;
  return res;
};

const {
  data: dataSource,
  run,
  loading,
  current,
  pageSize,
} = usePagination(queryData, {
  pagination: {
    currentKey: 'page',
    pageSizeKey: 'results',
  },
});

const pagination = computed(() => ({
  total: 200,
  current: current.value,
  pageSize: pageSize.value,
  
}));

const handleTableChange: TableProps['onChange'] = (
  pagination: { pageSize: number; current: number; },
  filters: any,
  sorter: any,
) => {
  run({
    results: pagination.pageSize,
    page: pagination?.current,
    sortField: sorter.field,
    sortOrder: sorter.order,
    ...filters,
  });
};
</script>
<style scoped>
.title{
  background-color: aqua;
}
</style>


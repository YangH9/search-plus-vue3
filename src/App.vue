<script setup lang="ts">
import { ref } from 'vue'
import MySearch from './components/MySearch.vue'

const form = ref({ search_value: '' })

const searchFilterParamsObject = ref([
  {
    title: '地域',
    code: 'region_name',
    data: [
      { key: '上海', value: '上海' },
      { key: '南京', value: '南京' },
      { key: '广州', value: '广州' },
      { key: '北京', value: '北京' },
      { key: '成都', value: '成都' }
    ]
  },
  {
    title: '云账号',
    code: 'account_id',
    data: [
      { key: '腾讯云', value: 1 },
      { key: '阿里云', value: 2 },
      { key: '华为云', value: 3 }
    ]
  },
  {
    title: '状态',
    code: 'status',
    data: [
      { key: '运行中', value: 'running' },
      { key: '已关机', value: 'stopped' },
      { key: '开机中', value: 'starting' }
    ]
  },
  {
    title: '计费模式',
    code: 'inst_bill_type',
    data: [
      { key: '包年包月', value: 'prepaid' },
      { key: '按量付费', value: 'postpaid' }
    ]
  },
  { title: '内网地址', code: 'intranet', data: [] },
  { title: '公网地址', code: 'extranet', data: [] },
  { title: '实例名称', code: 'instance_name', data: [] },
  { title: '实例ID', code: 'instance_id', data: [] }
])
</script>

<template>
  <MySearch v-model="form.search_value" :option="searchFilterParamsObject" />
</template>

<style lang="scss" scoped></style>

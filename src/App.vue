<script setup>
import { ref } from 'vue'
import MySearch from './components/MySearch/search.vue'

const form = ref({ search_value: {} })

const searchFilterParamsObject = ref([])
setTimeout(() => {
  searchFilterParamsObject.value = [
    {
      title: '地域',
      code: 'region_name',
      data: [
        { title: '上海', code: '上海' },
        { title: '南京', code: '南京' },
        { title: '广州', code: '广州' },
        { title: '北京', code: '北京' },
        { title: '成都', code: '成都' }
      ],
      select_type: 'select'
    },
    {
      title: '云账号',
      code: 'account_id',
      data: [
        { title: '腾讯云', code: 1 },
        { title: '阿里云', code: 2 },
        { title: '华为云', code: 3 }
      ],
      select_type: 'select'
    },
    {
      title: '状态',
      code: 'status',
      data: [
        { title: '运行中', code: 'running' },
        { title: '已关机', code: 'stopped' },
        { title: '开机中', code: 'starting' }
      ],
      select_type: 'select'
    },
    {
      title: '计费模式',
      code: 'inst_bill_type',
      data: [
        { title: '包年包月', code: 'prepaid' },
        { title: '按量付费', code: 'postpaid' }
      ],
      select_type: 'select'
    },
    { title: '内网地址', code: 'intranet', data: [], select_type: 'input' },
    { title: '公网地址', code: 'extranet', data: [], select_type: 'input' },
    { title: '实例名称', code: 'instance_name', data: [], select_type: 'input' },
    { title: '实例ID', code: 'instance_id', data: [], select_type: 'input' }
  ]
}, 2000)
</script>

<template>
  <div style="padding: 30px">
    <MySearch v-model="form.search_value" :selectData="searchFilterParamsObject" />
    <div>
      {{ form }}
    </div>
  </div>
</template>

<style lang="scss" scoped></style>

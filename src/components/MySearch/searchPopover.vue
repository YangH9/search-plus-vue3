<template>
  <div
    v-show="popoverOption.isShow"
    class="popover_box"
    :style="{ left: `${popoverOption.left}px`, top: `${popoverOption.top}px` }"
    @click.stop=""
  >
    <div class="popover" :style="{ marginLeft: `${popoverOption.marginLeft}px` }">
      <ul v-if="!popoverOption.isCheckBox" class="list">
        <li class="item is_disabled">选择资源属性进行过滤</li>
        <template v-for="(item, index) in checkboxOption" :key="index">
          <li v-if="!item.isUse" class="item" @click.stop="clickRadio(item)">
            {{ item.title }}
          </li>
        </template>
      </ul>
      <ul v-else class="list">
        <el-checkbox
          v-model="isCheckAll"
          class="item is_current"
          :indeterminate="isIndeterminate"
          @change="clickCheckbox"
        >
          全选
        </el-checkbox>
        <el-checkbox-group v-model="checkedData">
          <el-checkbox
            v-for="(item, index) in checkboxOption"
            :key="index"
            class="item"
            :value="item.code"
            :label="item.title"
            @change="clickCheckbox"
          />
        </el-checkbox-group>
      </ul>
      <div v-if="popoverOption.isCheckBox" class="popover_footer">
        <button
          class="btn"
          :class="popoverOption.isDisabled ? 'is_disabled' : ''"
          :disabled="popoverOption.isDisabled"
          @click.stop="clickSubmit"
        >
          确认
        </button>
        <button class="btn btn_close" @click.stop="clickReset">取消</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ElCheckbox, ElCheckboxGroup } from 'element-plus'
import { ref, computed, watch } from 'vue'

// 定义 props
const props = defineProps({
  // 配置项，isShow、isCheckBox、option
  popoverOption: {
    type: Object,
    default: () => ({})
  },
  // 多选的默认选中数据
  popoverData: {
    type: Array,
    default: () => []
  }
})

// 定义 emits
const emit = defineEmits(['clickRadio', 'clickCheckbox', 'clickSubmit', 'clickReset'])

// 下拉框元素数据
const checkboxOption = ref([])

// 下拉框选择数据
const checkedData = ref([])

// 初次传入的值，用于还原
const oldData = ref([])

// 多选事件效果 - 全选
const isCheckAll = computed({
  get() {
    return checkboxOption.value.length === checkedData.value.length
  },
  set(val) {
    checkedData.value = val ? checkboxOption.value.map(item => item.code) : []
  }
})

// 多选事件效果 - 半选状态
const isIndeterminate = computed(() => {
  return checkedData.value.length > 0 && checkedData.value.length < checkboxOption.value.length
})

// 监听变化，记录数据
watch(
  () => props.popoverOption,
  val => {
    if (checkboxOption.value !== val.option) {
      checkboxOption.value = val.option
      oldData.value = props.popoverData
      if (val.isCheckBox) {
        // 如果为多选框，先清空选择数据
        checkedData.value = []
      }
    }
  },
  { deep: true }
)

watch(
  () => props.popoverData,
  val => {
    checkedData.value = val
  },
  { deep: true }
)

// 下拉框弹窗，下拉框配置
const changeCheckBoxData = list => {
  checkedData.value = list
}

// 点击选项
const clickRadio = item => {
  // 单选状态，直接返回点击的参数
  emit('clickRadio', item)
}

// 点击多选
const clickCheckbox = () => {
  // 多选状态，数据同步至父组件
  emit('clickCheckbox', getCheckedObj(checkedData.value))
}

// 点击提交
const clickSubmit = () => {
  // 多选状态，点击提交返回数组
  emit('clickSubmit', getCheckedObj(checkedData.value))
}

// 点击还原
const clickReset = () => {
  // 多选状态，点击还原返回旧数据
  emit('clickReset', getCheckedObj(oldData.value))
}

// 提取多选数据
const getCheckedObj = data => {
  const checkboxOptionVal = checkboxOption.value
  return checkboxOptionVal.filter(item => data.filter(i => item.code === i).length > 0)
}

// 暴露方法给父组件
defineExpose({
  changeCheckBoxData
})
</script>

<style lang="scss" scoped>
.popover_box {
  user-select: none;
  z-index: 4000;
  position: fixed;

  .popover {
    border-radius: 4px;
    background-color: #fff;
    box-shadow: 0 0 16px 2px rgba(54, 58, 80, 0.5);
    min-width: 150px;
    color: rgba(0, 0, 0, 0.9);
    position: relative;
    padding: 8px;

    .list {
      padding: 0;
      list-style: none;
      color: rgba(0, 0, 0, 0.9);
      overflow-y: auto;
      margin: 0;
      max-height: 60vh;

      .item {
        margin-right: 0;
        width: 300px;
        font-size: var(--fontsize);
        padding: 6px 10px;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        color: #000;
        display: block;
        cursor: pointer;
        border-radius: 0 0 0 0;
        line-height: 1.5;

        &:hover {
          background-color: #ebeef2;
        }

        &.is_disabled {
          background-color: transparent;
          color: rgba(0, 0, 0, 0.25);
          cursor: not-allowed;
        }

        &.is_current {
          background-color: #ebeef2;
          cursor: pointer;
        }

        .check_label {
          pointer-events: none;
          position: relative;
          display: inline-block;
          vertical-align: middle;
          padding-left: 20px;
          margin: 0;
          min-height: 16px;
          color: rgba(0, 0, 0, 0.9);

          .checkbox {
            width: 16px;
            height: 16px;
            cursor: pointer;
            margin: 0;
            vertical-align: middle;
            outline: none;
            appearance: none;
            border: 1px solid #cfd5de;
            background-color: #fff;
            border-radius: 0;
            color: rgba(0, 0, 0, 0.9);
            position: absolute;
            top: 0;
            left: 0;

            &:hover,
            &:focus {
              border-color: #006eff;
            }
          }

          .check_span {
            line-height: 16px;
            cursor: pointer;
            font-size: var(--fontsize);
          }
        }
      }
    }

    .popover_footer {
      margin: 0 10px;
      border-top: 1px solid #e7eaef;
      padding: 10px 0;
      white-space: nowrap;

      .btn {
        height: 30px;
        min-width: 24px;
        padding: 0 20px;
        background-color: #006eff;
        color: #fff;
        border: 1px solid #006eff;
        line-height: 30px;
        text-align: center;
        display: inline-block;
        cursor: pointer;
        outline: 0 none;
        box-sizing: border-box;
        text-decoration: none;
        font-size: var(--fontsize);
        vertical-align: middle;
        white-space: nowrap;
        border-radius: 0;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;

        &:focus,
        &:hover {
          color: #fff;
          transition: all 0.3s ease-in-out;
          border-color: #2a86ff;
          background-color: #2a86ff;
        }

        &.is_disabled {
          border-color: #aacfff;
          background-color: #aacfff;
          color: #fff;
          cursor: not-allowed;

          &:focus,
          &:hover {
            border-color: #aacfff;
            background-color: #aacfff;
            color: #fff;
          }
        }

        &.btn_close {
          background-color: #fff;
          color: #006eff;
          border: none;
          margin-left: 5px;

          &:focus,
          &:hover {
            background-color: #ebeef2;
            border-color: #cfd5de;
            color: #006eff;
          }
        }
      }
    }
  }
}
</style>

<template>
  <div class="my_search" :style="{ width: width }">
    <div
      ref="searchBoxRef"
      class="search_box"
      :class="inputOption.isFocus ? 'active' : ''"
      @click.stop="onSearchFocus"
    >
      <div class="tag_group">
        <div v-for="(item, index) in tagData" :key="index" class="tag_item">
          <search-tag
            :key="index"
            :data="item"
            :index="index"
            :isFocus="inputOption.isFocus"
            :maxWidth="inputOption.maxWidth"
            :textWidth="getTextWidth"
            :showPopoverEle="showPopoverEle"
            @focus="tagFocus"
            @submit="tagSubmit"
            @delTag="onClickDelete"
          />
        </div>
        <div
          ref="inputBoxRef"
          class="input_box"
          :style="{ width: `${inputOption.width + 6}px` }"
          @click.stop=""
        >
          <input
            ref="inputRef"
            v-model="inputOption.value"
            class="search_input"
            type="text"
            :style="{ width: `${inputOption.width + 6}px` }"
            @input="onSearchInput"
            @keydown.delete="onSearchKeyDelete"
            @keydown.enter="onSearchKeyEnter"
          />
        </div>
      </div>
      <div class="tips">多个关键字用竖线 “|” 分隔，多个过滤标签用回车键分隔</div>
      <el-button
        v-show="tagData.length > 0"
        class="btn_del_all"
        :icon="CircleClose"
        @click.stop="onClickDelete('all')"
      />
    </div>
    <!-- 下拉框 -->
    <search-popover
      ref="popoverBoxRef"
      :popoverOption="popoverOption"
      :popoverData="popoverData"
      @clickRadio="clickPopoverRadio"
      @clickCheckbox="clickPopoverCheckbox"
      @clickSubmit="clickPopoverSubmit"
      @clickReset="clickPopoverReset"
    />
    <span ref="textWidthRef" class="text_width" />
  </div>
</template>

<script setup>
import { ref, reactive, watch, onMounted, onBeforeUnmount, nextTick } from 'vue'
import { ElButton } from 'element-plus'
import { CircleClose } from '@element-plus/icons-vue'
import SearchTag from './searchTag.vue'
import SearchPopover from './searchPopover.vue'

// 定义 props
const props = defineProps({
  modelValue: { type: Object, default: () => ({}) },
  selectData: { type: Array, default: () => [] },
  width: { type: String, default: '100%' }
})

// 定义 emits
const emit = defineEmits(['update:modelValue'])

// refs
const searchBoxRef = ref(null)
const inputBoxRef = ref(null)
const inputRef = ref(null)
const popoverBoxRef = ref(null)
const textWidthRef = ref(null)

// 搜索框的完整数据
const selectOption = ref([])

// 输入框配置关联项
const inputOption = reactive({
  maxWidth: 0,
  value: '',
  isFocus: false,
  width: 0
})

// 下拉框配置项
const popoverOption = reactive({
  isShow: false,
  isCheckBox: false,
  isDisabled: true,
  option: [],
  top: 0,
  left: 0,
  marginLeft: 0
})

// 下拉框选择数据
const popoverData = ref([])

// tag标签显示项，最终筛选的数据
const tagData = ref([])

// 当前操作的选项
const selectNow = ref({})

// 当前显示下拉框的元素
const showPopoverEle = ref('all')

// 监听 tagData 变化
watch(
  tagData,
  () => {
    const searchData = {}
    tagData.value.map(item => {
      searchData[item.code] = [...item.select.map(item => item.code)]
    })
    emit('update:modelValue', searchData)
  },
  { deep: true }
)

// 监听 selectData 变化
watch(
  () => props.selectData,
  val => {
    selectOption.value = val
    popoverOption.option = val
  },
  { immediate: true }
)

// 组件挂载时
onMounted(() => {
  if (searchBoxRef.value) {
    inputOption.maxWidth = searchBoxRef.value.offsetWidth
  }

  // 添加全局点击事件监听器
  window.addEventListener('click', handleWindowClick)
})

// 组件卸载前
onBeforeUnmount(() => {
  // 移除全局点击事件监听器
  window.removeEventListener('click', handleWindowClick)
})

// 全局点击处理函数
const handleWindowClick = () => {
  onSearchBlur()
}

// 搜索框获取焦点
const onSearchFocus = () => {
  showPopoverEle.value = 'all'
  if (!inputOption.isFocus) {
    inputOption.value = ''
  }

  nextTick(() => {
    if (inputRef.value) {
      inputRef.value.selectionStart = inputOption.value.length
    }
  })

  resetPopoverOption()
  setInputWidth()
  inputFocus()

  if (!inputOption.value) {
    showPopover()
  }
}

// 搜索框输入
const onSearchInput = () => {
  const value = inputOption.value
  const selectOptionVal = selectOption.value
  const tagDataVal = tagData.value
  let option = []
  const index = value.indexOf('：')

  // 截取输入内容
  if (index >= 0) {
    // 有冒号，匹配分类框，
    const word = value.slice(0, index)
    let selectArr = value.slice(index + 1).split('|')

    // 匹配到，则记录当前分类，进行下一步筛选，
    const selectNowItem = selectOptionVal.find(item => item.title === word)
    if (selectNowItem && selectNowItem.title) {
      if (selectNow.value.title !== selectNowItem.title) {
        clickPopoverRadio(selectNowItem)
      }

      // 匹配后面的值，如果是select类型按照选项值匹配，相反则全部填充
      if (selectNowItem.select_type === 'select') {
        selectArr = selectArr.map(item => selectNowItem.data.find(i => i.title === item.trim()))

        if (popoverBoxRef.value) {
          popoverBoxRef.value.changeCheckBoxData(selectArr.map(item => item?.code))
        }
      } else {
        selectArr = selectArr.map(item => ({
          title: item.trim(),
          code: item.trim()
        }))
      }

      selectNow.value.select = selectArr
    }
  } else {
    // 没有冒号，匹配字符，对下拉框进行筛选，清除选择值
    option = [
      ...selectOptionVal.filter(
        item => item.title.includes(value) && !tagDataVal.find(i => i.code === item.code)
      )
    ]
    selectNow.value = {}
  }

  if (option.length > 0) {
    popoverOption.option = option
    showPopover()
  }

  setInputWidth()
}

// 搜索框失去焦点
const onSearchBlur = () => {
  if (inputOption.isFocus) {
    inputOption.value = ''
    selectNow.value = {}
    setInputWidth()
    inputBlur()
    hidePopover()
  }
}

// 按下删除键
const onSearchKeyDelete = () => {
  if (inputOption.value === '') {
    deleteTag()
  }

  if (selectNow.value.code && inputOption.value.indexOf('：') < 0) {
    selectNow.value = {}
    resetPopoverOption()
  }
}

// 按下回车键
const onSearchKeyEnter = () => {
  const selectNowVal = selectNow.value
  if (selectNowVal.title) {
    clickPopoverSubmit(selectNowVal.select)
  }
}

// 点击删除按钮
const onClickDelete = index => {
  deleteTag(index)
}

// 点击类目
const clickPopoverRadio = item => {
  inputOption.value = `${item.title}：`
  setInputWidth()
  selectNow.value = item

  if (item.select_type === 'select') {
    popoverOption.isCheckBox = true
    popoverOption.option = item.data
  } else {
    popoverOption.isCheckBox = false
    popoverOption.isShow = false
    nextTick(() => {
      if (inputRef.value) {
        inputRef.value.focus()
      }
    })
  }

  setPopoverPosition()
}

// 点击多选
const clickPopoverCheckbox = data => {
  const val = `${selectNow.value.title}：${data.map(i => i.title).join(' | ')}`
  inputOption.value = val
  popoverOption.isDisabled = data.length === 0
  setInputWidth()
  inputFocus()
  showPopover()
}

// 点击提交
const clickPopoverSubmit = data => {
  tagData.value.push({
    ...selectNow.value,
    select: data
  })
  selectNow.value = {}
  raiseTag()
}

// 弹窗取消
const clickPopoverReset = () => {
  inputOption.value = ''
  selectNow.value = {}
  setInputWidth()
  raiseTag()
}

// 标签获取焦点
const tagFocus = index => {
  showPopoverEle.value = '' + index
  hidePopover()
}

// 标签修改提交
const tagSubmit = (data, index) => {
  tagData.value[index] = data
}

// 显示弹窗
const showPopover = () => {
  popoverOption.isShow = true
  setPopoverPosition()
}

// 隐藏弹窗
const hidePopover = () => {
  popoverOption.isShow = false
}

// 获取焦点
const inputFocus = () => {
  inputOption.isFocus = true
  nextTick(() => {
    if (inputRef.value) {
      inputRef.value.focus()
    }
  })
}

// 失去焦点
const inputBlur = () => {
  inputOption.isFocus = false
}

// 还原输入框配置
const resetInputOption = () => {
  inputOption.value = ''
  setInputWidth()
}

// 还原弹窗配置
const resetPopoverOption = () => {
  if (!selectNow.value.code) {
    const tagDataVal = tagData.value
    popoverOption.isShow = false
    popoverOption.isCheckBox = false
    popoverOption.option = selectOption.value.filter(item => {
      return tagDataVal.findIndex(tag => tag.code === item.code) < 0
    })
    selectNow.value = {}
  }
}

// 新增元素
const raiseTag = () => {
  resetInputOption()
  resetPopoverOption()
}

// 删除元素
const deleteTag = index => {
  if (typeof index === 'number') {
    // 有下标，删除单个
    tagData.value.splice(index, 1)
  } else if (typeof index === 'string') {
    // 删除全部
    tagData.value = []
  } else {
    // 无下标，删除最后一个
    if (tagData.value.length >= 1) {
      tagData.value.splice(tagData.value.length - 1, 1)
    }
  }

  nextTick(() => {
    resetPopoverOption()
    setInputWidth()
    inputFocus()
    showPopover()
  })
}

// 设置弹窗位置
const setPopoverPosition = () => {
  nextTick(() => {
    const marginLeft = selectNow.value.title ? getTextWidth(`${selectNow.value.title}：`) : 0

    if (!inputBoxRef.value || !popoverBoxRef.value) return

    const bodyWidth = document.body.offsetWidth
    const popoverWidth = popoverBoxRef.value.$el.offsetWidth + marginLeft
    const rect = inputBoxRef.value.getBoundingClientRect()
    const offsetLeft = rect.left
    const left = offsetLeft + popoverWidth < bodyWidth ? offsetLeft : bodyWidth - popoverWidth
    const top = searchBoxRef.value.getBoundingClientRect().top + searchBoxRef.value.offsetHeight
    // const top = rect.top + inputBoxRef.value.offsetHeight

    popoverOption.left = left
    popoverOption.top = top
    popoverOption.marginLeft = marginLeft
  })
}

// 设置元素宽度
const setInputWidth = () => {
  const value = inputOption.value
  const width = getTextWidth(value)

  if (!inputBoxRef.value || !inputBoxRef.value.offsetParent) return

  const offsetWidth = inputBoxRef.value.offsetParent.offsetWidth - 64
  inputOption.width = width > offsetWidth ? offsetWidth : width
}

// 获取字符宽度，返回像素值
const getTextWidth = str => {
  if (!textWidthRef.value) return 0
  textWidthRef.value.innerHTML = str
  return textWidthRef.value.offsetWidth
}
</script>

<style lang="scss" scoped>
.my_search {
  --fontsize: 14px;
  --text-height: 20px;
  --height: 32px;
  height: var(--height);

  .search_box {
    width: 100%;
    min-height: var(--height);
    height: var(--height);
    line-height: var(--text-height);
    overflow: hidden;
    background-color: #fff;
    text-align: left;
    position: relative;
    cursor: text;
    padding: 0 30px 0 8px;
    border: 1px solid #cfd5de;
    font-size: var(--fontsize);
    box-sizing: border-box;
    border-radius: 4px;
    display: inline-block;
    align-items: center;

    .tag_group {
      margin-top: 0;
      display: inline-block;
      padding-top: 2px;

      :deep(.tag_item) {
        pointer-events: none;
        display: inline-block;
        min-height: 20px;
        width: auto;
        position: relative;
        vertical-align: inherit;

        .tag_label {
          position: relative;
          background-color: #f3f4f7;
          color: #000;
          margin: 0 4px 2px 0;
          padding: 2px 8px;
          height: auto;
          line-height: calc(var(--height) - 10);
          vertical-align: middle;
          border: 1px solid #f3f4f7;
          border-radius: 4px;

          &,
          .tag_title,
          .tag_content {
            display: inline-block;
            white-space: nowrap;
            word-wrap: break-word;
            word-break: break-word;
          }

          .tag_title,
          .tag_content {
            font-size: var(--fontsize);
            vertical-align: top;
            max-width: 100px;
            overflow: hidden;
            text-overflow: ellipsis;
          }

          .tag_title {
            color: rgba(0, 0, 0, 0.4) !important;
          }

          .del {
            cursor: pointer;
            display: none;
            height: var(--text-height);
            line-height: var(--text-height);
            position: absolute;
            right: 6px;
            bottom: 2px;
          }
        }

        .tag_input_box {
          font-size: 0;
          line-height: normal;
          white-space: normal;
          display: inline-block;
          min-height: calc(var(--height) - 10);
          width: auto;
          position: relative;
          vertical-align: inherit;

          .tag_input {
            display: inline-block;
            position: relative;
            height: 100%;
            width: 280px;
            max-width: 764px;
            padding: 0px 8px;

            .textarea {
              font-family: inherit;
              margin: 0;
              overflow: auto;
              display: inline-block;
              box-sizing: border-box;
              vertical-align: middle;
              font-size: var(--fontsize);
              border-radius: 0;
              background-color: #fff;
              color: rgba(0, 0, 0, 0.9);
              transition: 0.2s ease-in-out;
              transition-property: color, background-color, border;
              outline: none;
              line-height: calc(var(--height) - 10);
              border: none;
              padding: 0 5px 0 0;
              position: absolute;
              top: 0px;
              left: 0px;
              height: 100%;
              resize: none;
              min-height: calc(var(--height) - 10);
            }
          }
        }
      }

      .input_box {
        display: inline-block;
        vertical-align: top;
        position: relative;
        width: 6px;
        max-width: 100%;
        height: var(--text-height);
        line-height: var(--text-height);

        .search_input {
          box-sizing: border-box;
          font-size: var(--fontsize);
          border-radius: 0;
          background-color: #fff;
          color: rgba(0, 0, 0, 0.9);
          transition: 0.2s ease-in-out;
          transition-property: color, background-color, border;
          position: static;
          border: none;
          // vertical-align: top;
          height: var(--text-height);
          line-height: var(--text-height);
          padding: 0 5px 0 0;
          width: 6px;
          max-width: 100%;
          display: none;
        }
      }
    }

    .tips {
      position: relative;
      text-wrap: nowrap;
      user-select: none;
      line-height: 21px;
      font-size: var(--fontsize);
      display: inline-block;
      color: rgba(0, 0, 0, 0.25);
    }

    .btn_del_all {
      width: 30px;
      height: var(--height);
      // line-height: calc(var(--height) * 1.5);
      bottom: 0;
      padding: 0 7px;
      background-color: #fff;
      vertical-align: middle;
      position: absolute;
      z-index: 4;
      cursor: pointer;
      border: none;
      text-align: center;
      right: 0px;
      outline: none;
      display: none;
    }

    &:hover,
    &:focus {
      border-color: #c6e2ff;
    }

    /* 获取焦点 */
    &.active {
      border-color: #3a8ee6;
      height: auto;
      background-color: #fff;
      width: 100%;
      z-index: 4001;

      .tag_group {
        white-space: normal;
        padding-top: 4px;

        :deep(.tag_item) {
          pointer-events: auto;

          .tag_label {
            padding: 2px 30px 2px 8px;
            margin: 0 4px 4px 0;

            .tag_title,
            .tag_content {
              display: inline;
              max-width: none;
              white-space: normal;
              vertical-align: middle;
            }

            .del {
              display: inline-block;
            }
          }
        }

        .input_box {
          .search_input {
            display: inline-block;
            outline: 0;
          }
        }
      }

      .btn_del_all {
        display: inline-block;
      }
    }
  }

  .text_width {
    position: absolute;
    top: -9999px;
    left: 0px;
    white-space: pre;
    font-size: var(--fontsize);
    font-family: inherit;
  }
}
</style>

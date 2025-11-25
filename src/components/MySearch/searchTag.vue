<template>
  <div v-if="!textareaOption.isFocus" class="tag_label" @click.stop="tagInputFocus">
    <span class="tag_title">{{ data.title }}：</span>
    <span class="tag_content">{{ textFilter(data.select) }}</span>
    <CircleClose class="del" @click.stop="delTag" />
  </div>
  <div v-else class="tag_input_box" @click.stop="">
    <div
      class="tag_input"
      :style="{ width: `${textareaOption.width}px`, maxWidth: `${maxWidth - 10}px` }"
    >
      <pre style="display: block; visibility: hidden">
        <div
          style="font-size: 12px; white-space: normal; line-height: 20px; max-height: 100px"
          :style="{ width: `${textareaOption.width}px`, maxWidth: `${maxWidth - 10}px` }"
        >
          {{ textareaOption.value }}
        </div>
        <br style="clear: both" />
      </pre>
      <textarea
        ref="textareaRef"
        v-model="textareaOption.value"
        class="textarea"
        :style="{ width: `${textareaOption.width}px`, maxWidth: `${maxWidth - 10}px` }"
        @focus="tagInputFocus"
        @input="tagInput"
        @keydown.delete="tagKeyDelete"
        @keydown.enter="tagKeyEnter"
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
  </div>
</template>

<script setup>
import { ref, reactive, watch, nextTick } from 'vue'
import { CircleClose } from '@element-plus/icons-vue'
import searchPopover from './searchPopover.vue'

// 定义 props
const props = defineProps({
  index: Number,
  isFocus: Boolean,
  maxWidth: Number,
  data: Object,
  textWidth: Function,
  showPopoverEle: String
})

// 定义 emits
const emit = defineEmits(['focus', 'submit', 'delTag'])

// refs
const textareaRef = ref(null)
const popoverBoxRef = ref(null)

// textarea 配置项
const textareaOption = reactive({
  isFocus: false,
  value: '',
  width: 0
})

// tag 数据
const tagData = ref({})

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

// 监听 isFocus 变化
watch(
  () => props.isFocus,
  val => {
    if (!val) {
      // 输入框失去焦点
      tagBlur()
    }
  }
)

// 监听 showPopoverEle 变化
watch(
  () => props.showPopoverEle,
  val => {
    if (val === 'all') {
      // 关闭所有状态
      tagBlur()
    } else {
      if (val === '' + props.index) {
        // 开启当前的修改状态
        tagFocus()
      } else {
        // 关闭当前的修改状态
        tagBlur()
      }
    }
  }
)

/**
 * 二次修改
 * 获取焦点，切换状态，显示下拉框，提交数据
 * 点击后提交当前下标，清除下拉框状态，按下标显示不同组件下的弹窗，
 * 外层组件的方法重写入，
 */

// 点击切换编辑
const tagInputFocus = () => {
  emit('focus', props.index)
  const tagDataVal = props.data
  tagData.value = tagDataVal
  const value = `${tagDataVal.title}：${tagDataVal.select.map((item, index) => (index === 0 ? `${item.title}` : ` | ${item.title}`)).join('')}`
  textareaOption.isFocus = true
  textareaOption.value = value
  popoverData.value = tagDataVal.select.map(item => item.code)

  nextTick(() => {
    if (textareaRef.value) {
      textareaRef.value.selectionStart = tagDataVal.title.length + 1
      textareaRef.value.selectionEnd = value.length
      textareaRef.value.focus()
    }
    tagFocus()
  })
}

// 获取焦点
const tagFocus = () => {
  setInputWidth()
  showPopover()
}

// 输入
const tagInput = () => {
  const value = textareaOption.value
  const tagDataVal = tagData.value
  const index = value.indexOf('：')

  // 截取输入内容
  if (index >= 0) {
    // 有冒号，匹配分类框，
    let selectArr = value.slice(index + 1).split('|')

    // 匹配到，则记录当前分类，进行下一步筛选，
    if (tagDataVal.title) {
      // 匹配后面的值，如果是select类型按照选项值匹配，相反则全部填充
      if (tagDataVal.select_type === 'select') {
        selectArr = selectArr.map(item => tagDataVal.data.find(i => i.title === item.trim()))

        if (popoverBoxRef.value) {
          popoverBoxRef.value.changeCheckBoxData(selectArr.map(item => item?.code).filter(i => i))
        }
      } else {
        selectArr = selectArr.map(item => ({ title: item.trim(), code: item.trim() }))
      }
    }

    popoverOption.isShow = true
    popoverOption.isCheckBox = tagDataVal.select_type === 'select'
    popoverOption.option = []
    popoverOption.option = tagDataVal.data
    tagData.value = {
      ...tagData.value,
      select: selectArr
    }
  } else {
    popoverOption.isShow = tagDataVal.select_type === 'select'
    popoverOption.isCheckBox = false
    popoverOption.option = [tagData.value]
  }

  setInputWidth()
}

// 失去焦点
const tagBlur = () => {
  textareaOption.isFocus = false
  popoverOption.isShow = false
}

// 提交
const tagSubmit = () => {
  const tagDataVal = tagData.value
  emit('submit', tagDataVal, props.index)
  tagBlur()
}

// 按下删除键
const tagKeyDelete = () => {
  if (textareaOption.value === '') {
    delTag()
  }
}

// 按下回车键
const tagKeyEnter = e => {
  e.preventDefault()
  const tagDataVal = tagData.value

  if (tagDataVal === props.data || JSON.stringify(tagDataVal) === '{}') {
    tagBlur()
  } else if (tagDataVal.title) {
    clickPopoverSubmit(tagDataVal.select)
  }

  return false
}

// 点击类目
const clickPopoverRadio = item => {
  textareaOption.value = `${item.title}：`
  setInputWidth()

  if (item.select_type === 'select') {
    popoverOption.isCheckBox = true
    popoverOption.option = item.data
  } else {
    popoverOption.isCheckBox = false
    popoverOption.isShow = false
    nextTick(() => {
      if (textareaRef.value) {
        textareaRef.value.focus()
      }
    })
  }

  showPopover()
}

// 点击多选
const clickPopoverCheckbox = data => {
  const val = `${tagData.value.title}：${data.map(i => i.title).join(' | ')}`
  textareaOption.value = val
  popoverOption.isDisabled = data.length === 0
  tagFocus()
}

// 点击提交
const clickPopoverSubmit = data => {
  tagData.value = {
    ...tagData.value,
    select: data
  }
  tagSubmit()
}

// 弹窗取消
const clickPopoverReset = () => {
  tagBlur()
}

// 显示弹窗
const showPopover = () => {
  const tagDataVal = tagData.value
  popoverOption.isShow = props.data.select_type === 'select'
  popoverOption.isCheckBox = props.data.select_type === 'select'
  popoverOption.option = []
  popoverOption.option = tagDataVal.data
  setPopoverPosition()
}

// 弹窗配置
const setPopoverPosition = () => {
  nextTick(() => {
    const tagDataVal = tagData.value
    const marginLeft = tagDataVal.title ? props.textWidth(`${tagDataVal.title}：`) : 0

    if (!textareaRef.value) return

    const inputBox = textareaRef.value
    const bodyWidth = document.body.offsetWidth

    // 查找 popover 元素
    const popoverElement = document.querySelector('.popover_box')
    if (!popoverElement) return

    const popoverWidth = popoverElement.offsetWidth + marginLeft
    const offsetLeft = inputBox.getBoundingClientRect().left
    const left = offsetLeft + popoverWidth < bodyWidth ? offsetLeft : bodyWidth - popoverWidth
    const top = inputBox.getBoundingClientRect().top + inputBox.offsetHeight

    popoverOption.marginLeft = marginLeft
    popoverOption.left = left
    popoverOption.top = top
  })
}

// 设置输入框宽度
const setInputWidth = () => {
  textareaOption.width = props.textWidth(textareaOption.value) + 21
}

// 删除
const delTag = () => {
  emit('delTag', props.index)
}

// 文本过滤
const textFilter = arr => {
  let str = ''
  arr.map((item, index) => {
    str += index === 0 ? `${item.title}` : ` | ${item.title}`
  })
  return str
}
</script>

<style lang="scss" scoped></style>

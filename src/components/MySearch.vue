<script setup lang="tsx">
const props = defineProps({ value: Object, option: Object })
defineEmits(['update:value'])
console.log(props)
</script>

<template>
  <!-- <el-input placeholder="多个关键字用竖线 “|” 分隔，多个过滤标签用回车键分隔" /> -->
  多个关键字用竖线 “|” 分隔，多个过滤标签用回车键分隔
  {{ option }}
  <div class="el-select">
    <div
      class="el-select__wrapper is-filterable el-tooltip__trigger el-tooltip__trigger"
      tabindex="-1"
    >
      <!--v-if-->
      <div class="el-select__selection is-near">
        <div class="el-select__selected-item">
          <span
            class="el-tag is-closable el-tag--info el-tag--default el-tag--light"
            style="max-width: 628px"
            ><span class="el-tag__content"><span class="el-select__tags-text">HTML</span></span
            ><i class="el-icon el-tag__close"
              ><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1024 1024">
                <path
                  fill="currentColor"
                  d="M764.288 214.592 512 466.88 259.712 214.592a31.936 31.936 0 0 0-45.12 45.12L466.752 512 214.528 764.224a31.936 31.936 0 1 0 45.12 45.184L512 557.184l252.288 252.288a31.936 31.936 0 0 0 45.12-45.12L557.12 512.064l252.288-252.352a31.936 31.936 0 1 0-45.12-45.184z"
                ></path></svg></i
          ></span>
        </div>
        <div class="el-select__selected-item">
          <span
            class="el-tag is-closable el-tag--info el-tag--default el-tag--light"
            style="max-width: 628px"
            ><span class="el-tag__content"><span class="el-select__tags-text">CSS</span></span
            ><i class="el-icon el-tag__close"
              ><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1024 1024">
                <path
                  fill="currentColor"
                  d="M764.288 214.592 512 466.88 259.712 214.592a31.936 31.936 0 0 0-45.12 45.12L466.752 512 214.528 764.224a31.936 31.936 0 1 0 45.12 45.184L512 557.184l252.288 252.288a31.936 31.936 0 0 0 45.12-45.12L557.12 512.064l252.288-252.352a31.936 31.936 0 1 0-45.12-45.184z"
                ></path></svg></i
          ></span>
        </div>
        <!--v-if-->
        <div class="el-select__selected-item el-select__input-wrapper">
          <input
            type="text"
            class="el-select__input"
            autocomplete="off"
            tabindex="0"
            role="combobox"
            spellcheck="false"
            aria-activedescendant="el-id-1024-989"
            aria-controls="el-id-1024-987"
            aria-expanded="false"
            aria-autocomplete="none"
            aria-haspopup="listbox"
            style="min-width: 11px"
            id="el-id-1024-1176"
          /><span aria-hidden="true" class="el-select__input-calculator"></span>
        </div>
        <!--v-if-->
      </div>
      <div class="el-select__suffix">
        <i class="el-icon el-select__caret el-select__icon"
          ><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1024 1024">
            <path
              fill="currentColor"
              d="M831.872 340.864 512 652.672 192.128 340.864a30.59 30.59 0 0 0-42.752 0 29.12 29.12 0 0 0 0 41.6L489.664 714.24a32 32 0 0 0 44.672 0l340.288-331.712a29.12 29.12 0 0 0 0-41.728 30.59 30.59 0 0 0-42.752 0z"
            ></path></svg></i
        ><!--v-if--><!--v-if-->
      </div>
    </div>
  </div>
</template>

<style scoped>
:root {
  --el-color-white: #ffffff;
  --el-color-black: #000000;
  --el-color-primary-rgb: 64, 158, 255;
  --el-color-success-rgb: 103, 194, 58;
  --el-color-warning-rgb: 230, 162, 60;
  --el-color-danger-rgb: 245, 108, 108;
  --el-color-error-rgb: 245, 108, 108;
  --el-color-info-rgb: 144, 147, 153;
  --el-font-size-extra-large: 20px;
  --el-font-size-large: 18px;
  --el-font-size-medium: 16px;
  --el-font-size-base: 14px;
  --el-font-size-small: 13px;
  --el-font-size-extra-small: 12px;
  --el-font-family:
    'Helvetica Neue', Helvetica, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑',
    Arial, sans-serif;
  --el-font-weight-primary: 500;
  --el-font-line-height-primary: 24px;
  --el-index-normal: 1;
  --el-index-top: 1000;
  --el-index-popper: 2000;
  --el-border-radius-base: 4px;
  --el-border-radius-small: 2px;
  --el-border-radius-round: 20px;
  --el-border-radius-circle: 100%;
  --el-transition-duration: 0.3s;
  --el-transition-duration-fast: 0.2s;
  --el-transition-function-ease-in-out-bezier: cubic-bezier(0.645, 0.045, 0.355, 1);
  --el-transition-function-fast-bezier: cubic-bezier(0.23, 1, 0.32, 1);
  --el-transition-all: all var(--el-transition-duration)
    var(--el-transition-function-ease-in-out-bezier);
  --el-transition-fade: opacity var(--el-transition-duration)
    var(--el-transition-function-fast-bezier);
  --el-transition-md-fade:
    transform var(--el-transition-duration) var(--el-transition-function-fast-bezier),
    opacity var(--el-transition-duration) var(--el-transition-function-fast-bezier);
  --el-transition-fade-linear: opacity var(--el-transition-duration-fast) linear;
  --el-transition-border: border-color var(--el-transition-duration-fast)
    var(--el-transition-function-ease-in-out-bezier);
  --el-transition-box-shadow: box-shadow var(--el-transition-duration-fast)
    var(--el-transition-function-ease-in-out-bezier);
  --el-transition-color: color var(--el-transition-duration-fast)
    var(--el-transition-function-ease-in-out-bezier);
  --el-component-size-large: 40px;
  --el-component-size: 32px;
  --el-component-size-small: 24px;
}

:root {
  color-scheme: light;
  --el-color-primary: #409eff;
  --el-color-primary-light-3: rgb(121, 187, 255);
  --el-color-primary-light-5: rgb(160, 207, 255);
  --el-color-primary-light-7: rgb(198, 226, 255);
  --el-color-primary-light-8: rgb(217, 236, 255);
  --el-color-primary-light-9: rgb(236, 245, 255);
  --el-color-primary-dark-2: rgb(51, 126, 204);
  --el-color-success: #67c23a;
  --el-color-success-light-3: rgb(149, 212, 117);
  --el-color-success-light-5: rgb(179, 225, 157);
  --el-color-success-light-7: rgb(209, 237, 196);
  --el-color-success-light-8: rgb(225, 243, 216);
  --el-color-success-light-9: rgb(240, 249, 235);
  --el-color-success-dark-2: rgb(82, 155, 46);
  --el-color-warning: #e6a23c;
  --el-color-warning-light-3: rgb(238, 190, 119);
  --el-color-warning-light-5: rgb(243, 209, 158);
  --el-color-warning-light-7: rgb(248, 227, 197);
  --el-color-warning-light-8: rgb(250, 236, 216);
  --el-color-warning-light-9: rgb(253, 246, 236);
  --el-color-warning-dark-2: rgb(184, 130, 48);
  --el-color-danger: #f56c6c;
  --el-color-danger-light-3: rgb(248, 152, 152);
  --el-color-danger-light-5: rgb(250, 182, 182);
  --el-color-danger-light-7: rgb(252, 211, 211);
  --el-color-danger-light-8: rgb(253, 226, 226);
  --el-color-danger-light-9: rgb(254, 240, 240);
  --el-color-danger-dark-2: rgb(196, 86, 86);
  --el-color-error: #f56c6c;
  --el-color-error-light-3: rgb(248, 152, 152);
  --el-color-error-light-5: rgb(250, 182, 182);
  --el-color-error-light-7: rgb(252, 211, 211);
  --el-color-error-light-8: rgb(253, 226, 226);
  --el-color-error-light-9: rgb(254, 240, 240);
  --el-color-error-dark-2: rgb(196, 86, 86);
  --el-color-info: #909399;
  --el-color-info-light-3: rgb(177, 179, 184);
  --el-color-info-light-5: rgb(200, 201, 204);
  --el-color-info-light-7: rgb(222, 223, 224);
  --el-color-info-light-8: rgb(233, 233, 235);
  --el-color-info-light-9: rgb(244, 244, 245);
  --el-color-info-dark-2: rgb(115, 118, 122);
  --el-bg-color: #ffffff;
  --el-bg-color-page: #f2f3f5;
  --el-bg-color-overlay: #ffffff;
  --el-text-color-primary: #303133;
  --el-text-color-regular: #606266;
  --el-text-color-secondary: #909399;
  --el-text-color-placeholder: #a8abb2;
  --el-text-color-disabled: #c0c4cc;
  --el-border-color: #dcdfe6;
  --el-border-color-light: #e4e7ed;
  --el-border-color-lighter: #ebeef5;
  --el-border-color-extra-light: #f2f6fc;
  --el-border-color-dark: #d4d7de;
  --el-border-color-darker: #cdd0d6;
  --el-fill-color: #f0f2f5;
  --el-fill-color-light: #f5f7fa;
  --el-fill-color-lighter: #fafafa;
  --el-fill-color-extra-light: #fafcff;
  --el-fill-color-dark: #ebedf0;
  --el-fill-color-darker: #e6e8eb;
  --el-fill-color-blank: #ffffff;
  --el-box-shadow: 0px 12px 32px 4px rgba(0, 0, 0, 0.04), 0px 8px 20px rgba(0, 0, 0, 0.08);
  --el-box-shadow-light: 0px 0px 12px rgba(0, 0, 0, 0.12);
  --el-box-shadow-lighter: 0px 0px 6px rgba(0, 0, 0, 0.12);
  --el-box-shadow-dark:
    0px 16px 48px 16px rgba(0, 0, 0, 0.08), 0px 12px 32px rgba(0, 0, 0, 0.12),
    0px 8px 16px -8px rgba(0, 0, 0, 0.16);
  --el-disabled-bg-color: var(--el-fill-color-light);
  --el-disabled-text-color: var(--el-text-color-placeholder);
  --el-disabled-border-color: var(--el-border-color-light);
  --el-overlay-color: rgba(0, 0, 0, 0.8);
  --el-overlay-color-light: rgba(0, 0, 0, 0.7);
  --el-overlay-color-lighter: rgba(0, 0, 0, 0.5);
  --el-mask-color: rgba(255, 255, 255, 0.9);
  --el-mask-color-extra-light: rgba(255, 255, 255, 0.3);
  --el-border-width: 1px;
  --el-border-style: solid;
  --el-border-color-hover: var(--el-text-color-disabled);
  --el-border: var(--el-border-width) var(--el-border-style) var(--el-border-color);
  --el-svg-monochrome-grey: var(--el-border-color);
}

.el-select {
  display: inline-block;
  position: relative;
  vertical-align: middle;
  width: var(--el-select-width);
}

.el-select__wrapper {
  display: flex;
  align-items: center;
  position: relative;
  box-sizing: border-box;
  cursor: pointer;
  text-align: left;
  font-size: 14px;
  padding: 4px 12px;
  gap: 6px;
  min-height: 32px;
  line-height: 24px;
  border-radius: var(--el-border-radius-base);
  background-color: var(--el-fill-color-blank);
  transition: var(--el-transition-duration);
  transform: translateZ(0);
  box-shadow: 0 0 0 1px var(--el-border-color) inset;
}

.el-select__wrapper.is-filterable {
  cursor: text;
}

.el-select__wrapper.is-focused {
  box-shadow: 0 0 0 1px var(--el-color-primary) inset;
}

.el-select__wrapper.is-hovering:not(.is-focused) {
  box-shadow: 0 0 0 1px var(--el-border-color-hover) inset;
}

.el-select__wrapper.is-disabled {
  cursor: not-allowed;
  background-color: var(--el-fill-color-light);
  color: var(--el-text-color-placeholder);
  box-shadow: 0 0 0 1px var(--el-select-disabled-border) inset;
}

.el-select__wrapper.is-disabled:hover {
  box-shadow: 0 0 0 1px var(--el-select-disabled-border) inset;
}

.el-select__wrapper.is-disabled.is-focus {
  box-shadow: 0 0 0 1px var(--el-input-focus-border-color) inset;
}

.el-select__wrapper.is-disabled .el-select__selected-item {
  color: var(--el-select-disabled-color);
}

.el-select__wrapper.is-disabled .el-select__caret,
.el-select__wrapper.is-disabled .el-tag,
.el-select__wrapper.is-disabled input {
  cursor: not-allowed;
}

.el-select__wrapper.is-disabled .el-select__prefix,
.el-select__wrapper.is-disabled .el-select__suffix {
  pointer-events: none;
}

.el-select__prefix,
.el-select__suffix {
  display: flex;
  align-items: center;
  flex-shrink: 0;
  gap: 6px;
  color: var(--el-input-icon-color, var(--el-text-color-placeholder));
}

.el-select__caret {
  color: var(--el-select-input-color);
  font-size: var(--el-select-input-font-size);
  transition: var(--el-transition-duration);
  transform: rotate(0);
  cursor: pointer;
}

.el-select__caret.is-reverse {
  transform: rotate(180deg);
}

.el-select__clear {
  cursor: pointer;
}

.el-select__clear:hover {
  color: var(--el-select-close-hover-color);
}

.el-select__selection {
  position: relative;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  flex: 1;
  min-width: 0;
  gap: 6px;
}

.el-select__selection.is-near {
  margin-left: -8px;
}

.el-select__selection .el-tag {
  cursor: pointer;
  border-color: transparent;
}

.el-select__selection .el-tag.el-tag--plain {
  border-color: var(--el-tag-border-color);
}

.el-select__selection .el-tag .el-tag__content {
  min-width: 0;
}

.el-select__selected-item {
  display: flex;
  flex-wrap: wrap;
  -webkit-user-select: none;
  user-select: none;
}

.el-select__tags-text {
  display: block;
  line-height: normal;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.el-select__placeholder {
  position: absolute;
  z-index: -1;
  display: block;
  top: 50%;
  transform: translateY(-50%);
  width: 100%;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  color: var(--el-input-text-color, var(--el-text-color-regular));
}

.el-select__placeholder.is-transparent {
  -webkit-user-select: none;
  user-select: none;
  color: var(--el-text-color-placeholder);
}

.el-select__popper.el-popper {
  background: var(--el-bg-color-overlay);
  border: 1px solid var(--el-border-color-light);
  box-shadow: var(--el-box-shadow-light);
}

.el-select__popper.el-popper .el-popper__arrow:before {
  border: 1px solid var(--el-border-color-light);
}

.el-select__popper.el-popper[data-popper-placement^='top'] .el-popper__arrow:before {
  border-top-color: transparent;
  border-left-color: transparent;
}

.el-select__popper.el-popper[data-popper-placement^='bottom'] .el-popper__arrow:before {
  border-bottom-color: transparent;
  border-right-color: transparent;
}

.el-select__popper.el-popper[data-popper-placement^='left'] .el-popper__arrow:before {
  border-left-color: transparent;
  border-bottom-color: transparent;
}

.el-select__popper.el-popper[data-popper-placement^='right'] .el-popper__arrow:before {
  border-right-color: transparent;
  border-top-color: transparent;
}

.el-select__input-wrapper {
  flex: 1;
}

.el-select__input-wrapper.is-hidden {
  position: absolute;
  opacity: 0;
  z-index: -1;
}

.el-select__input {
  border: none;
  outline: none;
  padding: 0;
  color: var(--el-select-multiple-input-color);
  font-size: inherit;
  font-family: inherit;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  height: 24px;
  width: 100%;
  background-color: transparent;
}

.el-select__input-calculator {
  position: absolute;
  left: 0;
  top: 0;
  max-width: 100%;
  visibility: hidden;
  white-space: pre;
  overflow: hidden;
}

.el-select--large .el-select__wrapper {
  gap: 6px;
  padding: 8px 16px;
  min-height: 40px;
  line-height: 24px;
  font-size: 14px;
}

.el-select--large .el-select__selection {
  gap: 6px;
}

.el-select--large .el-select__selection.is-near {
  margin-left: -8px;
}

.el-select--large .el-select__prefix,
.el-select--large .el-select__suffix {
  gap: 6px;
}

.el-select--large .el-select__input {
  height: 24px;
}

.el-select--small .el-select__wrapper {
  gap: 4px;
  padding: 2px 8px;
  min-height: 24px;
  line-height: 20px;
  font-size: 12px;
}

.el-select--small .el-select__selection {
  gap: 4px;
}

.el-select--small .el-select__selection.is-near {
  margin-left: -6px;
}

.el-select--small .el-select__prefix,
.el-select--small .el-select__suffix {
  gap: 4px;
}

.el-select--small .el-select__input {
  height: 20px;
}
</style>

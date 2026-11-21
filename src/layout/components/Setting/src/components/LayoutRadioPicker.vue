<script lang="ts" setup>
import { useAppStore } from '@/store/modules/app'
import { useDesign } from '@/hooks/web/useDesign'

defineOptions({ name: 'LayoutRadioPicker' })

const { getPrefixCls } = useDesign()

const prefixCls = getPrefixCls('layout-radio-picker')

const appStore = useAppStore()

const layout = computed(() => appStore.getLayout)
</script>

<template>
  <div :class="prefixCls" class="flex flex-wrap justify-center gap-10px">
    <div
      :class="[
        `${prefixCls}__classic`,
        'relative w-62px h-50px cursor-pointer',
        {
          'is-acitve': layout === 'classic'
        }
      ]"
      @click="appStore.setLayout('classic')"
    ></div>
    <div
      :class="[
        `${prefixCls}__top-left`,
        'relative w-62px h-50px cursor-pointer',
        {
          'is-acitve': layout === 'topLeft'
        }
      ]"
      @click="appStore.setLayout('topLeft')"
    ></div>
    <div
      :class="[
        `${prefixCls}__top`,
        'relative w-62px h-50px cursor-pointer',
        {
          'is-acitve': layout === 'top'
        }
      ]"
      @click="appStore.setLayout('top')"
    ></div>
    <div
      :class="[
        `${prefixCls}__cut-menu`,
        'relative w-62px h-50px cursor-pointer',
        {
          'is-acitve': layout === 'cutMenu'
        }
      ]"
      @click="appStore.setLayout('cutMenu')"
    >
      <div class="absolute left-[10%] top-0 h-full w-[33%] bg-[var(--el-fill-color)]"></div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
$prefix-cls: #{$namespace}-layout-radio-picker;

.#{$prefix-cls} {
  &__classic,
  &__top-left,
  &__top,
  &__cut-menu {
    overflow: hidden;
    background-color: var(--el-fill-color);
    border: 1px solid var(--el-border-color);
    border-radius: 9px;
    transition:
      border-color 0.2s ease,
      box-shadow 0.2s ease,
      transform 0.2s ease;

    &:hover {
      border-color: var(--el-color-primary-light-5);
      transform: translateY(-1px);
    }
  }

  &__classic {
    &::before {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 1;
      width: 33%;
      height: 100%;
      background-color: #273352;
      content: '';
    }

    &::after {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 25%;
      background-color: #fff;
      content: '';
    }
  }

  &__top-left {
    &::before {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 1;
      width: 100%;
      height: 33%;
      background-color: #273352;
      content: '';
    }

    &::after {
      position: absolute;
      top: 0;
      left: 0;
      width: 33%;
      height: 100%;
      background-color: #fff;
      content: '';
    }
  }

  &__top {
    &::before {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 1;
      width: 100%;
      height: 33%;
      background-color: #273352;
      content: '';
    }
  }

  &__cut-menu {
    &::before {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 1;
      width: 100%;
      height: 33%;
      background-color: #273352;
      content: '';
    }
  }

  .is-acitve {
    border-color: var(--el-color-primary);
    box-shadow: 0 0 0 2px rgba(var(--el-color-primary-rgb), 0.16);
  }
}
</style>

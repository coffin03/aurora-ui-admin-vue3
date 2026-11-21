<script lang="ts" setup>
import { PropType } from 'vue'
import { propTypes } from '@/utils/propTypes'
import { useDesign } from '@/hooks/web/useDesign'

defineOptions({ name: 'ColorRadioPicker' })

const { getPrefixCls } = useDesign()

const prefixCls = getPrefixCls('color-radio-picker')

const props = defineProps({
  schema: {
    type: Array as PropType<string[]>,
    default: () => []
  },
  modelValue: propTypes.string.def('')
})

const emit = defineEmits(['update:modelValue', 'change'])

const colorVal = ref(props.modelValue)

watch(
  () => props.modelValue,
  (val: string) => {
    if (val === unref(colorVal)) return
    colorVal.value = val
  }
)

// 监听
watch(
  () => colorVal.value,
  (val: string) => {
    emit('update:modelValue', val)
    emit('change', val)
  }
)
</script>

<template>
  <div :class="prefixCls" class="flex flex-wrap gap-10px">
    <span
      v-for="(item, i) in schema"
      :key="`radio-${i}`"
      :class="[`${prefixCls}__item`, { 'is-active': colorVal === item }]"
      :style="{
        background: item
      }"
      @click="colorVal = item"
    >
      <Icon
        v-if="colorVal === item"
        :size="13"
        :color="item === '#fff' ? 'var(--el-color-primary)' : '#fff'"
        icon="ep:check"
      />
    </span>
  </div>
</template>

<style lang="scss" scoped>
$prefix-cls: #{$namespace}-color-radio-picker;

.#{$prefix-cls} {
  &__item {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 26px;
    height: 26px;
    cursor: pointer;
    border-radius: 50%;
    box-shadow: inset 0 0 0 1px rgba(0, 0, 0, 0.08);
    transition:
      transform 0.2s ease,
      box-shadow 0.2s ease;

    &:hover {
      transform: scale(1.08);
    }
  }

  .is-active {
    box-shadow:
      0 0 0 2px var(--app-bg-color),
      0 0 0 4px var(--el-color-primary),
      inset 0 0 0 1px rgba(0, 0, 0, 0.08);
  }
}
</style>

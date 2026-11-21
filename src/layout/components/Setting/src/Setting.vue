<script lang="ts" setup>
import { ElMessage } from 'element-plus'
import { useClipboard, useCssVar } from '@vueuse/core'

import { CACHE_KEY, useCache } from '@/hooks/web/useCache'
import { useDesign } from '@/hooks/web/useDesign'

import { setCssVar, trim } from '@/utils'
import { colorIsDark, hexToRGB, lighten } from '@/utils/color'
import { useAppStore } from '@/store/modules/app'
import { ThemeSwitch } from '@/layout/components/ThemeSwitch'
import ColorRadioPicker from './components/ColorRadioPicker.vue'
import InterfaceDisplay from './components/InterfaceDisplay.vue'
import LayoutRadioPicker from './components/LayoutRadioPicker.vue'

defineOptions({ name: 'Setting' })

const { t } = useI18n()
const appStore = useAppStore()

const { getPrefixCls } = useDesign()
const prefixCls = getPrefixCls('setting')
const layout = computed(() => appStore.getLayout)
const drawer = ref(false)

// 主题色相关
const systemTheme = ref(appStore.getTheme.elColorPrimary)

const setSystemTheme = (color: string) => {
  setCssVar('--el-color-primary', color)
  appStore.setTheme({ elColorPrimary: color })
  const leftMenuBgColor = useCssVar('--left-menu-bg-color', document.documentElement)
  setMenuTheme(trim(unref(leftMenuBgColor)))
}

// 头部主题相关
const headerTheme = ref(appStore.getTheme.topHeaderBgColor || '')

const setHeaderTheme = (color: string) => {
  const isDarkColor = colorIsDark(color)
  const textColor = isDarkColor ? '#fff' : 'inherit'
  const textHoverColor = isDarkColor ? lighten(color!, 6) : '#f6f6f6'
  const topToolBorderColor = isDarkColor ? color : '#eee'
  setCssVar('--top-header-bg-color', color)
  setCssVar('--top-header-text-color', textColor)
  setCssVar('--top-header-hover-color', textHoverColor)
  appStore.setTheme({
    topHeaderBgColor: color,
    topHeaderTextColor: textColor,
    topHeaderHoverColor: textHoverColor,
    topToolBorderColor
  })
  if (unref(layout) === 'top') {
    setMenuTheme(color)
  }
}

// 菜单主题相关
const menuTheme = ref(appStore.getTheme.leftMenuBgColor || '')

const setMenuTheme = (color: string) => {
  const primaryColor = useCssVar('--el-color-primary', document.documentElement)
  const isDarkColor = colorIsDark(color)
  const theme: Recordable = {
    // 左侧菜单边框颜色
    leftMenuBorderColor: isDarkColor ? 'inherit' : '#eee',
    // 左侧菜单背景颜色
    leftMenuBgColor: color,
    // 左侧菜单浅色背景颜色
    leftMenuBgLightColor: isDarkColor ? lighten(color!, 6) : color,
    // 左侧菜单选中背景颜色
    leftMenuBgActiveColor: isDarkColor
      ? 'var(--el-color-primary)'
      : hexToRGB(unref(primaryColor), 0.1),
    // 左侧菜单收起选中背景颜色
    leftMenuCollapseBgActiveColor: isDarkColor
      ? 'var(--el-color-primary)'
      : hexToRGB(unref(primaryColor), 0.1),
    // 左侧菜单字体颜色
    leftMenuTextColor: isDarkColor ? '#bfcbd9' : '#333',
    // 左侧菜单选中字体颜色
    leftMenuTextActiveColor: isDarkColor ? '#fff' : 'var(--el-color-primary)',
    // logo字体颜色
    logoTitleTextColor: isDarkColor ? '#fff' : 'inherit',
    // logo边框颜色
    logoBorderColor: isDarkColor ? color : '#eee'
  }
  appStore.setTheme(theme)
  appStore.setCssVarTheme()
}
if (layout.value === 'top' && !appStore.getIsDark) {
  headerTheme.value = '#fff'
  setHeaderTheme('#fff')
}

// 监听layout变化，重置一些主题色
watch(
  () => layout.value,
  (n) => {
    if (n === 'top' && !appStore.getIsDark) {
      headerTheme.value = '#fff'
      setHeaderTheme('#fff')
    } else {
      setMenuTheme(unref(menuTheme))
    }
  }
)

// 拷贝
const copyConfig = async () => {
  const { copy, copied, isSupported } = useClipboard({
    source: `
      // 面包屑
      breadcrumb: ${appStore.getBreadcrumb},
      // 面包屑图标
      breadcrumbIcon: ${appStore.getBreadcrumbIcon},
      // 折叠图标
      hamburger: ${appStore.getHamburger},
      // 全屏图标
      screenfull: ${appStore.getScreenfull},
      // 尺寸图标
      size: ${appStore.getSize},
      // 多语言图标
      locale: ${appStore.getLocale},
      // 消息图标
      message: ${appStore.getMessage},
      // 标签页
      tagsView: ${appStore.getTagsView},
      // 标签页
      tagsViewImmerse: ${appStore.getTagsViewImmerse},
      // 标签页图标
      tagsViewIcon: ${appStore.getTagsViewIcon},
      // logo
      logo: ${appStore.getLogo},
      // 菜单手风琴
      uniqueOpened: ${appStore.getUniqueOpened},
      // 固定header
      fixedHeader: ${appStore.getFixedHeader},
      // 页脚
      footer: ${appStore.getFooter},
      // 灰色模式
      greyMode: ${appStore.getGreyMode},
      // layout布局
      layout: '${appStore.getLayout}',
      // 暗黑模式
      isDark: ${appStore.getIsDark},
      // 组件尺寸
      currentSize: '${appStore.getCurrentSize}',
      // 主题相关
      theme: {
        // 主题色
        elColorPrimary: '${appStore.getTheme.elColorPrimary}',
        // 左侧菜单边框颜色
        leftMenuBorderColor: '${appStore.getTheme.leftMenuBorderColor}',
        // 左侧菜单背景颜色
        leftMenuBgColor: '${appStore.getTheme.leftMenuBgColor}',
        // 左侧菜单浅色背景颜色
        leftMenuBgLightColor: '${appStore.getTheme.leftMenuBgLightColor}',
        // 左侧菜单选中背景颜色
        leftMenuBgActiveColor: '${appStore.getTheme.leftMenuBgActiveColor}',
        // 左侧菜单收起选中背景颜色
        leftMenuCollapseBgActiveColor: '${appStore.getTheme.leftMenuCollapseBgActiveColor}',
        // 左侧菜单字体颜色
        leftMenuTextColor: '${appStore.getTheme.leftMenuTextColor}',
        // 左侧菜单选中字体颜色
        leftMenuTextActiveColor: '${appStore.getTheme.leftMenuTextActiveColor}',
        // logo字体颜色
        logoTitleTextColor: '${appStore.getTheme.logoTitleTextColor}',
        // logo边框颜色
        logoBorderColor: '${appStore.getTheme.logoBorderColor}',
        // 头部背景颜色
        topHeaderBgColor: '${appStore.getTheme.topHeaderBgColor}',
        // 头部字体颜色
        topHeaderTextColor: '${appStore.getTheme.topHeaderTextColor}',
        // 头部悬停颜色
        topHeaderHoverColor: '${appStore.getTheme.topHeaderHoverColor}',
        // 头部边框颜色
        topToolBorderColor: '${appStore.getTheme.topToolBorderColor}'
      }
    `
  })
  if (!isSupported) {
    ElMessage.error(t('setting.copyFailed'))
  } else {
    await copy()
    if (unref(copied)) {
      ElMessage.success(t('setting.copySuccess'))
    }
  }
}

// 清空缓存
const clear = () => {
  const { wsCache } = useCache()
  wsCache.delete(CACHE_KEY.LAYOUT)
  wsCache.delete(CACHE_KEY.THEME)
  wsCache.delete(CACHE_KEY.IS_DARK)
  window.location.reload()
}
</script>

<template>
  <div
    :class="prefixCls"
    class="fixed right-0 top-[45%] z-[1200] cursor-pointer"
    @click="drawer = true"
  >
    <Icon :class="`${prefixCls}__icon`" :size="18" color="#fff" icon="ep:setting" />
  </div>

  <ElDrawer
    v-model="drawer"
    class="setting-drawer"
    :z-index="4000"
    direction="rtl"
    size="380px"
  >
    <template #header>
      <div class="flex items-center gap-10px">
        <span class="setting-drawer__badge">
          <Icon :size="15" color="#fff" icon="ep:setting" />
        </span>
        <span class="text-15px font-600">{{ t('setting.projectSetting') }}</span>
      </div>
    </template>

    <div class="setting-drawer__content">
      <!-- 主题 -->
      <section class="setting-block">
        <div class="setting-block__title">{{ t('setting.theme') }}</div>
        <div class="setting-block__center">
          <ThemeSwitch />
        </div>
      </section>

      <!-- 布局 -->
      <section class="setting-block">
        <div class="setting-block__title">{{ t('setting.layout') }}</div>
        <LayoutRadioPicker />
      </section>

      <!-- 系统主题 -->
      <section class="setting-block">
        <div class="setting-block__title">{{ t('setting.systemTheme') }}</div>
        <ColorRadioPicker
          v-model="systemTheme"
          :schema="[
            '#6366f1',
            '#409eff',
            '#009688',
            '#536dfe',
            '#ff5c93',
            '#ee4f12',
            '#0096c7',
            '#9c27b0'
          ]"
          @change="setSystemTheme"
        />
      </section>

      <!-- 头部主题 -->
      <section class="setting-block">
        <div class="setting-block__title">{{ t('setting.headerTheme') }}</div>
        <ColorRadioPicker
          v-model="headerTheme"
          :schema="[
            '#fff',
            '#151515',
            '#5172dc',
            '#e74c3c',
            '#24292e',
            '#394664',
            '#009688',
            '#383f45'
          ]"
          @change="setHeaderTheme"
        />
      </section>

      <!-- 菜单主题 -->
      <section v-if="layout !== 'top'" class="setting-block">
        <div class="setting-block__title">{{ t('setting.menuTheme') }}</div>
        <ColorRadioPicker
          v-model="menuTheme"
          :schema="[
            '#fff',
            '#001529',
            '#212121',
            '#273352',
            '#191b24',
            '#383f45',
            '#001628',
            '#344058'
          ]"
          @change="setMenuTheme"
        />
      </section>

      <!-- 界面显示 -->
      <section class="setting-block">
        <div class="setting-block__title">{{ t('setting.interfaceDisplay') }}</div>
        <InterfaceDisplay />
      </section>
    </div>

    <template #footer>
      <div class="flex gap-10px">
        <ElButton class="!m-0 flex-1" type="primary" @click="copyConfig">
          {{ t('setting.copy') }}
        </ElButton>
        <ElButton class="!m-0 flex-1" type="danger" plain @click="clear">
          {{ t('setting.clearAndReset') }}
        </ElButton>
      </div>
    </template>
  </ElDrawer>
</template>

<style lang="scss" scoped>
$prefix-cls: #{$namespace}-setting;

.#{$prefix-cls} {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 42px;
  height: 46px;
  background: linear-gradient(
    140deg,
    var(--el-color-primary-light-3),
    var(--el-color-primary) 45%,
    var(--el-color-primary-dark-2)
  );
  border-radius: 13px 0 0 13px;
  box-shadow: -4px 8px 22px rgba(var(--el-color-primary-rgb), 0.35);
  transition:
    width 0.25s ease,
    box-shadow 0.25s ease;

  &::before {
    position: absolute;
    inset: 1px;
    background: linear-gradient(180deg, rgba(255, 255, 255, 0.25), transparent 60%);
    border-radius: 12px 0 0 12px;
    content: '';
    pointer-events: none;
  }

  &:hover {
    width: 46px;
    box-shadow: -6px 10px 28px rgba(var(--el-color-primary-rgb), 0.45);
  }

  &__icon {
    position: relative;
    transition: transform 0.4s ease;
  }

  &:hover &__icon {
    transform: rotate(90deg);
  }
}
</style>

<style lang="scss">
.setting-drawer {
  background-color: var(--app-card-bg-color);

  .el-drawer__header {
    padding: 18px 20px;
    margin-bottom: 0;
    border-bottom: 1px solid var(--app-border-color-light);
  }

  .el-drawer__body {
    padding: 16px 20px;
    background-color: var(--app-card-bg-color);
  }

  .el-drawer__footer {
    padding: 14px 20px;
    border-top: 1px solid var(--app-border-color-light);
  }

  &__badge {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 28px;
    height: 28px;
    background: linear-gradient(140deg, var(--el-color-primary-light-3), var(--el-color-primary));
    border-radius: 9px;
    box-shadow: 0 4px 10px rgba(var(--el-color-primary-rgb), 0.3);
  }

  &__content {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  .setting-block {
    padding: 14px 16px 16px;
    background-color: var(--app-bg-color);
    border: 1px solid var(--app-border-color-light);
    border-radius: 12px;

    &__title {
      display: flex;
      gap: 6px;
      align-items: center;
      margin-bottom: 12px;
      font-size: 13px;
      font-weight: 600;
      color: var(--app-text-primary);

      &::before {
        width: 3px;
        height: 12px;
        background: linear-gradient(
          180deg,
          var(--el-color-primary-light-3),
          var(--el-color-primary)
        );
        border-radius: 2px;
        content: '';
      }
    }

    &__center {
      display: flex;
      justify-content: center;
    }
  }
}
</style>

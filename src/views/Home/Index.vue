<template>
  <div>
    <!-- 欢迎横幅 -->
    <div class="home-hero" :style="heroStyle">
      <div class="home-hero__circle home-hero__circle--one"></div>
      <div class="home-hero__circle home-hero__circle--two"></div>
      <el-skeleton :loading="loading" animated>
        <div class="relative z-10 flex flex-wrap items-center justify-between gap-20px">
          <div class="flex items-center">
            <el-avatar :src="avatar" :size="64" class="home-hero__avatar mr-16px">
              <img src="@/assets/imgs/avatar.gif" alt="" />
            </el-avatar>
            <div>
              <div class="text-22px font-600 text-white">
                {{ t('workplace.welcome') }} {{ username }} {{ t('workplace.happyDay') }}
              </div>
              <div class="mt-8px text-13px text-white/70">
                {{ t('workplace.toady') }}，20℃ - 32℃！
              </div>
            </div>
          </div>
          <div class="flex flex-wrap items-center gap-12px">
            <div class="home-hero__stat">
              <div class="text-12px text-white/70">{{ t('workplace.project') }}</div>
              <CountTo
                class="mt-4px block text-24px font-700 text-white"
                :start-val="0"
                :end-val="totalSate.project"
                :duration="2600"
              />
            </div>
            <div class="home-hero__stat">
              <div class="text-12px text-white/70">{{ t('workplace.toDo') }}</div>
              <CountTo
                class="mt-4px block text-24px font-700 text-white"
                :start-val="0"
                :end-val="totalSate.todo"
                :duration="2600"
              />
            </div>
            <div class="home-hero__stat">
              <div class="text-12px text-white/70">{{ t('workplace.access') }}</div>
              <CountTo
                class="mt-4px block text-24px font-700 text-white"
                :start-val="0"
                :end-val="totalSate.access"
                :duration="2600"
              />
            </div>
          </div>
        </div>
      </el-skeleton>
    </div>

    <el-row class="mt-16px" :gutter="16" justify="space-between">
      <el-col :xl="16" :lg="16" :md="24" :sm="24" :xs="24" class="mb-16px">
        <!-- 项目列表 -->
        <el-card shadow="never">
          <template #header>
            <div class="flex items-center justify-between">
              <span class="font-600">{{ t('workplace.project') }}</span>
            </div>
          </template>
          <el-skeleton :loading="loading" animated>
            <el-row :gutter="12">
              <el-col
                v-for="(item, index) in projects"
                :key="`card-${index}`"
                :xl="8"
                :lg="8"
                :md="8"
                :sm="24"
                :xs="24"
                class="mb-12px"
              >
                <div class="home-project h-full">
                  <div class="flex items-center">
                    <span class="home-project__icon">
                      <Icon :icon="item.icon" :size="20" />
                    </span>
                    <span class="truncate text-15px font-600">{{ item.name }}</span>
                  </div>
                  <div class="mt-10px truncate text-12px text-[var(--app-text-secondary)]">
                    {{ t(item.message) }}
                  </div>
                  <div
                    class="mt-12px flex justify-between gap-8px text-12px text-[var(--app-text-secondary)]"
                  >
                    <span class="truncate">{{ item.personal }}</span>
                    <span class="flex-shrink-0">{{ formatTime(item.time, 'yyyy-MM-dd') }}</span>
                  </div>
                </div>
              </el-col>
            </el-row>
          </el-skeleton>
        </el-card>

        <!-- 数据概览 -->
        <el-card shadow="never" class="mt-16px">
          <el-skeleton :loading="loading" animated>
            <el-row :gutter="16">
              <el-col :xl="10" :lg="10" :md="24" :sm="24" :xs="24">
                <Echart :options="pieOptionsData" :height="280" />
              </el-col>
              <el-col :xl="14" :lg="14" :md="24" :sm="24" :xs="24">
                <Echart :options="barOptionsData" :height="280" />
              </el-col>
            </el-row>
          </el-skeleton>
        </el-card>
      </el-col>

      <el-col :xl="8" :lg="8" :md="24" :sm="24" :xs="24" class="mb-16px">
        <!-- 快捷入口 -->
        <el-card shadow="never">
          <template #header>
            <div class="flex items-center justify-between">
              <span class="font-600">{{ t('workplace.shortcutOperation') }}</span>
            </div>
          </template>
          <el-skeleton :loading="loading" animated>
            <el-row>
              <el-col v-for="item in shortcut" :key="`team-${item.name}`" :span="8" class="mb-8px">
                <div class="home-shortcut" @click="setWatermark(item.name)">
                  <Icon :icon="item.icon" :size="22" />
                  <span class="mt-6px truncate text-12px">{{ item.name }}</span>
                </div>
              </el-col>
            </el-row>
          </el-skeleton>
        </el-card>

        <!-- 通知公告 -->
        <el-card shadow="never" class="mt-16px">
          <template #header>
            <div class="flex items-center justify-between">
              <span class="font-600">{{ t('workplace.notice') }}</span>
              <el-link type="primary" :underline="false">{{ t('action.more') }}</el-link>
            </div>
          </template>
          <el-skeleton :loading="loading" animated>
            <div v-for="(item, index) in notice" :key="`dynamics-${index}`" class="home-notice">
              <el-avatar :src="avatar" :size="32" class="mr-12px flex-shrink-0">
                <img src="@/assets/imgs/avatar.gif" alt="" />
              </el-avatar>
              <div class="min-w-0 flex-1">
                <div class="flex items-center text-13px leading-5">
                  <el-tag
                    size="small"
                    effect="light"
                    :type="item.type === '公告' ? 'warning' : 'primary'"
                    class="mr-6px flex-shrink-0"
                  >
                    {{ item.type }}
                  </el-tag>
                  <span class="truncate">
                    <Highlight :keys="item.keys.map((v) => t(v))">{{ item.title }}</Highlight>
                  </span>
                </div>
                <div class="mt-6px text-12px text-[var(--app-text-secondary)]">
                  {{ formatTime(item.date, 'yyyy-MM-dd') }}
                </div>
              </div>
            </div>
          </el-skeleton>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>
<script lang="ts" setup>
import { set } from 'lodash-es'
import { EChartsOption } from 'echarts'
import { formatTime } from '@/utils'

import { useUserStore } from '@/store/modules/user'
import { useAppStore } from '@/store/modules/app'
import { hexToRGB, isHexColor, mixWithBlack, mixWithWhite } from '@/utils/color'
import { useWatermark } from '@/hooks/web/useWatermark'
import type { WorkplaceTotal, Project, Notice, Shortcut } from './types'
import { pieOptions, barOptions } from './echarts-data'

defineOptions({ name: 'Home' })

const { t } = useI18n()
const userStore = useUserStore()
const appStore = useAppStore()
const { setWatermark } = useWatermark()
const loading = ref(true)
const avatar = userStore.getUser.avatar
const username = userStore.getUser.nickname
const pieOptionsData = reactive<EChartsOption>(pieOptions) as EChartsOption

// 品牌色跟随主题配置
const brandColor = computed(() => {
  const color = appStore.getTheme.elColorPrimary
  return isHexColor(color) ? color : '#6366f1'
})

// 欢迎横幅渐变由主题色派生
const heroStyle = computed(() => {
  const color = brandColor.value
  const rgb = hexToRGB(color).replace(/[^\d,]/g, '')
  return {
    background: `linear-gradient(120deg, ${mixWithBlack(color, 16)} 0%, ${color} 58%, ${mixWithWhite(
      color,
      20
    )} 100%)`,
    boxShadow: `0 14px 32px rgba(${rgb}, 0.24)`
  }
})
// 获取统计数
let totalSate = reactive<WorkplaceTotal>({
  project: 0,
  access: 0,
  todo: 0
})

const getCount = async () => {
  const data = {
    project: 40,
    access: 2340,
    todo: 10
  }
  totalSate = Object.assign(totalSate, data)
}

// 获取项目数
let projects = reactive<Project[]>([])
const getProject = async () => {
  const data = [
    {
      name: 'Aurora Admin',
      icon: 'logos:vue',
      message: '前端工程：Vue3 + Element Plus',
      personal: '前端',
      time: new Date()
    },
    {
      name: 'ruoyi-vue-pro',
      icon: 'akar-icons:github-fill',
      message: '后端工程：Spring Boot 单体架构',
      personal: '后端',
      time: new Date()
    }
  ]
  projects = Object.assign(projects, data)
}

// 获取通知公告
let notice = reactive<Notice[]>([])
const getNotice = async () => {
  const data = [
    {
      title: '欢迎使用 Aurora Admin 管理后台',
      type: '公告',
      keys: ['公告', 'Aurora'],
      date: new Date()
    },
    {
      title: '可在「项目配置」中切换主题色、头部主题、菜单主题与暗色模式',
      type: '通知',
      keys: ['通知', '项目配置'],
      date: new Date()
    },
    {
      title: '本项目基于 MIT 许可证开源，详见 LICENSE 与 NOTICE',
      type: '通知',
      keys: ['通知', 'MIT'],
      date: new Date()
    },
    {
      title: '使用前请先启动后端服务，并确认默认登录租户与后端一致',
      type: '公告',
      keys: ['公告', '租户'],
      date: new Date()
    }
  ]
  notice = Object.assign(notice, data)
}

// 获取快捷入口
let shortcut = reactive<Shortcut[]>([])

const getShortcut = async () => {
  const data = [
    {
      name: 'Github',
      icon: 'akar-icons:github-fill',
      url: 'github.io'
    },
    {
      name: 'Vue',
      icon: 'logos:vue',
      url: 'vuejs.org'
    },
    {
      name: 'Vite',
      icon: 'vscode-icons:file-type-vite',
      url: 'https://vitejs.dev/'
    },
    {
      name: 'Angular',
      icon: 'logos:angular-icon',
      url: 'github.io'
    },
    {
      name: 'React',
      icon: 'logos:react',
      url: 'github.io'
    },
    {
      name: 'Webpack',
      icon: 'logos:webpack',
      url: 'github.io'
    }
  ]
  shortcut = Object.assign(shortcut, data)
}

// 用户来源
const getUserAccessSource = async () => {
  const data = [
    { value: 335, name: 'analysis.directAccess' },
    { value: 310, name: 'analysis.mailMarketing' },
    { value: 234, name: 'analysis.allianceAdvertising' },
    { value: 135, name: 'analysis.videoAdvertising' },
    { value: 1548, name: 'analysis.searchEngines' }
  ]
  set(pieOptionsData, 'color', [
    brandColor.value,
    '#8b5cf6',
    '#22d3ee',
    '#34d399',
    '#fbbf24'
  ])
  // 跟随暗黑模式调整描边与标题配色
  const isDark = appStore.getIsDark
  set(pieOptionsData, 'series[0].itemStyle.borderColor', isDark ? '#171d2c' : '#fff')
  set(pieOptionsData, 'title.textStyle.color', isDark ? '#e5e7eb' : '#1f2437')
  set(pieOptionsData, 'legend.textStyle.color', isDark ? '#c2c8d6' : '#4b5563')
  set(
    pieOptionsData,
    'legend.data',
    data.map((v) => t(v.name))
  )
  pieOptionsData!.series![0].data = data.map((v) => {
    return {
      name: t(v.name),
      value: v.value
    }
  })
}
const barOptionsData = reactive<EChartsOption>(barOptions) as EChartsOption

// 周活跃量
const getWeeklyUserActivity = async () => {
  const data = [
    { value: 13253, name: 'analysis.monday' },
    { value: 34235, name: 'analysis.tuesday' },
    { value: 26321, name: 'analysis.wednesday' },
    { value: 12340, name: 'analysis.thursday' },
    { value: 24643, name: 'analysis.friday' },
    { value: 1322, name: 'analysis.saturday' },
    { value: 1324, name: 'analysis.sunday' }
  ]
  const isDark = appStore.getIsDark
  const axisTextColor = isDark ? '#8b93a7' : '#6b7280'
  set(barOptionsData, 'title.textStyle.color', isDark ? '#e5e7eb' : '#1f2437')
  set(barOptionsData, 'xAxis.axisLabel.color', axisTextColor)
  set(barOptionsData, 'xAxis.axisLine.lineStyle.color', isDark ? '#2a3245' : '#e5e7eb')
  set(barOptionsData, 'yAxis.axisLabel.color', axisTextColor)
  set(barOptionsData, 'yAxis.splitLine.lineStyle.color', isDark ? '#20283a' : '#eef0f5')
  set(
    barOptionsData,
    'xAxis.data',
    data.map((v) => t(v.name))
  )
  set(barOptionsData, 'series', [
    {
      name: t('analysis.activeQuantity'),
      data: data.map((v) => v.value),
      type: 'bar',
      barWidth: 18,
      itemStyle: {
        borderRadius: [6, 6, 0, 0],
        color: {
          type: 'linear',
          x: 0,
          y: 0,
          x2: 0,
          y2: 1,
          colorStops: [
            { offset: 0, color: mixWithWhite(brandColor.value, 30) },
            { offset: 1, color: brandColor.value }
          ]
        }
      }
    }
  ])
}

const getAllApi = async () => {
  await Promise.all([
    getCount(),
    getProject(),
    getNotice(),
    getShortcut(),
    getUserAccessSource(),
    getWeeklyUserActivity()
  ])
  loading.value = false
}

// 主题色 / 暗黑模式变化时刷新图表配色
watch(
  () => [appStore.getTheme.elColorPrimary, appStore.getIsDark],
  () => {
    getUserAccessSource()
    getWeeklyUserActivity()
  }
)

getAllApi()
</script>

<style lang="scss" scoped>
.home-hero {
  position: relative;
  padding: 28px;
  overflow: hidden;
  border-radius: var(--app-radius-lg);

  &__circle {
    position: absolute;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 50%;
    filter: blur(2px);

    &--one {
      top: -70px;
      right: -30px;
      width: 220px;
      height: 220px;
    }

    &--two {
      right: 180px;
      bottom: -100px;
      width: 180px;
      height: 180px;
      background: rgba(255, 255, 255, 0.07);
    }
  }

  &__avatar {
    border: 2px solid rgba(255, 255, 255, 0.35);
    box-shadow: 0 6px 16px rgba(30, 27, 75, 0.2);
  }

  &__stat {
    min-width: 112px;
    padding: 12px 18px;
    background: rgba(255, 255, 255, 0.14);
    border: 1px solid rgba(255, 255, 255, 0.16);
    border-radius: var(--app-radius-md);
    backdrop-filter: blur(6px);
  }
}

.home-project {
  padding: 16px;
  background-color: var(--app-card-bg-color);
  border: 1px solid var(--app-border-color);
  border-radius: var(--app-radius-md);
  transition:
    border-color var(--transition-time-02),
    box-shadow var(--transition-time-02),
    transform var(--transition-time-02);

  &:hover {
    border-color: var(--el-color-primary-light-8);
    box-shadow: var(--app-shadow-sm);
    transform: translateY(-2px);
  }

  &__icon {
    display: flex;
    flex-shrink: 0;
    align-items: center;
    justify-content: center;
    width: 34px;
    height: 34px;
    margin-right: 10px;
    background-color: var(--app-brand-color-soft);
    border-radius: 9px;
  }
}

.home-shortcut {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 14px 8px;
  color: var(--app-text-regular);
  cursor: pointer;
  border-radius: var(--app-radius-md);
  transition:
    background-color var(--transition-time-02),
    color var(--transition-time-02);

  &:hover {
    color: var(--el-color-primary);
    background-color: var(--el-fill-color-light);
  }
}

.home-notice {
  display: flex;
  padding: 14px 0;
  border-bottom: 1px solid var(--app-border-color-light);

  &:first-child {
    padding-top: 0;
  }

  &:last-child {
    padding-bottom: 0;
    border-bottom: none;
  }
}
</style>
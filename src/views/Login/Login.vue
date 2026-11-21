<template>
  <div :class="prefixCls" class="relative h-[100%] overflow-auto bg-[var(--app-bg-color)]">
    <div class="mx-auto h-full flex">
      <!-- 左侧品牌展示区 -->
      <div
        :class="`${prefixCls}__left`"
        class="relative flex-1 flex flex-col overflow-hidden p-56px lt-xl:hidden"
        :style="leftStyle"
      >
        <!-- Logo + 系统标题 -->
        <div class="relative z-10 flex items-center text-white">
          <img
            alt=""
            class="mr-12px h-44px w-44px rounded-[10px] bg-white/10 p-2px"
            src="@/assets/imgs/logo.png"
          />
          <span class="text-20px font-bold tracking-wide">
            {{ underlineToHump(appStore.getTitle) }}
          </span>
        </div>

        <!-- 欢迎语 -->
        <div class="relative z-10 my-auto">
          <h1 class="text-42px font-700 leading-tight text-white">
            {{ t('login.welcome') }}
          </h1>
          <p class="mt-16px max-w-480px text-15px leading-7 text-white/75">
            {{ t('login.message') }}
          </p>
          <div class="mt-40px flex flex-wrap items-center gap-12px">
            <span
              class="h-34px flex items-center rounded-full bg-white/12 px-16px text-13px text-white/85 backdrop-blur-[4px]"
            >
              开箱即用的企业级中后台
            </span>
            <span
              class="h-34px flex items-center rounded-full bg-white/12 px-16px text-13px text-white/85 backdrop-blur-[4px]"
            >
              权限 · 数据可视化 · 多租户
            </span>
          </div>
        </div>

        <div class="relative z-10 text-12px text-white/50">
          © {{ new Date().getFullYear() }} {{ underlineToHump(appStore.getTitle) }}
        </div>

        <!-- 背景装饰 -->
        <div
          class="absolute -right-70px -top-80px h-320px w-320px rounded-full bg-white/10 blur-2xl"
        ></div>
        <div
          class="absolute -bottom-90px right-110px h-260px w-260px rounded-full bg-white/10 blur-2xl"
        ></div>
        <img
          alt=""
          class="absolute bottom-80px right-30px w-220px opacity-15"
          src="@/assets/svgs/login-box-bg.svg"
        />
      </div>

      <!-- 右侧表单区 -->
      <div
        class="relative w-full flex flex-col bg-[var(--app-card-bg-color)] p-32px lt-xl:px-20px lt-sm:px-16px xl:w-560px"
      >
        <!-- 主题、语言选择 -->
        <div class="flex items-center justify-between">
          <div class="flex items-center at-xl:hidden">
            <img
              alt=""
              class="mr-10px h-40px w-40px rounded-[8px]"
              src="@/assets/imgs/logo.png"
            />
            <span class="text-18px font-bold">{{ underlineToHump(appStore.getTitle) }}</span>
          </div>
          <div class="ml-auto flex items-center space-x-10px">
            <ThemeSwitch />
            <LocaleDropdown />
          </div>
        </div>

        <!-- 登录表单 -->
        <div class="flex flex-1 items-center justify-center py-40px">
          <Transition appear enter-active-class="animate__animated animate__fadeIn">
            <div class="w-full max-w-420px">
              <!-- 账号登录 -->
              <LoginForm class="login-form" />
              <!-- 手机登录 -->
              <MobileForm class="login-form" />
              <!-- 二维码登录 -->
              <QrCodeForm class="login-form" />
              <!-- 注册 -->
              <RegisterForm class="login-form" />
              <!-- 三方登录 -->
              <SSOLoginVue class="login-form" />
              <!-- 忘记密码 -->
              <ForgetPasswordForm class="login-form" />
            </div>
          </Transition>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts" setup>
import { underlineToHump } from '@/utils'

import { useDesign } from '@/hooks/web/useDesign'
import { useAppStore } from '@/store/modules/app'
import { isHexColor, mixWithBlack, mixWithWhite } from '@/utils/color'
import { ThemeSwitch } from '@/layout/components/ThemeSwitch'
import { LocaleDropdown } from '@/layout/components/LocaleDropdown'

import {
  LoginForm,
  MobileForm,
  QrCodeForm,
  RegisterForm,
  SSOLoginVue,
  ForgetPasswordForm
} from './components'

defineOptions({ name: 'Login' })

const { t } = useI18n()
const appStore = useAppStore()
const { getPrefixCls } = useDesign()
const prefixCls = getPrefixCls('login')

// 左侧品牌区渐变跟随主题色
const leftStyle = computed(() => {
  const themeColor = appStore.getTheme.elColorPrimary
  const color = isHexColor(themeColor) ? themeColor : '#6366f1'
  return {
    background: `linear-gradient(135deg, ${mixWithBlack(color, 16)} 0%, ${color} 48%, ${mixWithWhite(
      color,
      18
    )} 100%)`
  }
})
</script>

<style lang="scss" scoped>
$prefix-cls: #{$namespace}-login;

.#{$prefix-cls} {
  &__left {
    &::before {
      position: absolute;
      inset: 0;
      background-image:
        linear-gradient(rgba(255, 255, 255, 0.06) 1px, transparent 1px),
        linear-gradient(90deg, rgba(255, 255, 255, 0.06) 1px, transparent 1px);
      background-size: 44px 44px;
      mask-image: radial-gradient(circle at 30% 30%, #000 0%, transparent 75%);
      content: '';
    }
  }
}
</style>

<style lang="scss">
.login-form {
  .el-form-item {
    margin-bottom: 20px;
  }

  .el-form-item__label {
    padding-bottom: 6px;
    font-size: 13px;
    font-weight: 500;
    color: var(--app-text-regular);
  }

  .el-input__wrapper {
    padding: 4px 12px;
    background-color: var(--el-fill-color-light);
    box-shadow: 0 0 0 1px transparent inset;
  }

  .el-input__wrapper:hover {
    box-shadow: 0 0 0 1px var(--el-border-color) inset;
  }

  .el-input__wrapper.is-focus {
    background-color: var(--app-card-bg-color);
    box-shadow: 0 0 0 1px var(--el-color-primary) inset;
  }

  .el-button--large {
    height: 44px;
    font-size: 15px;
    border-radius: 10px;
  }

  .el-divider__text {
    font-size: 12px;
    color: var(--app-text-secondary);
    background-color: var(--app-card-bg-color);
  }
}

.dark .login-form .el-divider__text {
  background-color: var(--login-bg-color);
}
</style>
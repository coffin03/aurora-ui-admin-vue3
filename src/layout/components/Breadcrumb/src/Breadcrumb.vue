<script lang="tsx">
import { ElBreadcrumb, ElBreadcrumbItem } from 'element-plus'
import { ArrowRight } from '@element-plus/icons-vue'
import { ref, watch, computed, unref, defineComponent, TransitionGroup } from 'vue'
import { useRouter } from 'vue-router'
import { usePermissionStore } from '@/store/modules/permission'
import { filterBreadcrumb } from './helper'
import { filter, treeToList } from '@/utils/tree'
import type { RouteLocationNormalizedLoaded, RouteMeta } from 'vue-router'

import { Icon } from '@/components/Icon'
import { useAppStore } from '@/store/modules/app'
import { useDesign } from '@/hooks/web/useDesign'

const { getPrefixCls } = useDesign()

const prefixCls = getPrefixCls('breadcrumb')

const appStore = useAppStore()

// 面包屑图标
const breadcrumbIcon = computed(() => appStore.getBreadcrumbIcon)

export default defineComponent({
  name: 'Breadcrumb',
  setup() {
    const { currentRoute } = useRouter()

    const { t } = useI18n()

    const levelList = ref<AppRouteRecordRaw[]>([])

    const permissionStore = usePermissionStore()

    const menuRouters = computed(() => {
      const routers = permissionStore.getRouters
      return filterBreadcrumb(routers)
    })

    const getBreadcrumb = () => {
      const currentPath = currentRoute.value.matched.slice(-1)[0].path

      levelList.value = filter<AppRouteRecordRaw>(unref(menuRouters), (node: AppRouteRecordRaw) => {
        return node.path === currentPath
      })
    }

    const renderBreadcrumb = () => {
      const breadcrumbList = treeToList<AppRouteRecordRaw[]>(unref(levelList))
      return breadcrumbList.map((v, index) => {
        const disabled = !v.redirect || v.redirect === 'noredirect'
        const meta = v.meta as RouteMeta
        const isLast = index === breadcrumbList.length - 1
        const content =
          meta?.icon && breadcrumbIcon.value ? (
            <div class="flex items-center">
              <Icon icon={meta.icon} class="mr-[2px]" svgClass="inline-block"></Icon>
              {t(v?.meta?.title)}
            </div>
          ) : (
            t(v?.meta?.title)
          )
        return (
          <ElBreadcrumbItem to={{ path: disabled ? '' : v.path }} key={v.name}>
            {isLast ? (
              <span class="bc-current">
                <span class="bc-dot"></span>
                {content}
              </span>
            ) : (
              content
            )}
          </ElBreadcrumbItem>
        )
      })
    }

    watch(
      () => currentRoute.value,
      (route: RouteLocationNormalizedLoaded) => {
        if (route.path.startsWith('/redirect/')) {
          return
        }
        getBreadcrumb()
      },
      {
        immediate: true
      }
    )

    return () => (
      <ElBreadcrumb
        separator="/"
        separator-icon={ArrowRight}
        class={`${prefixCls} flex items-center h-full ml-[6px]`}
      >
        <TransitionGroup appear enter-active-class="animate__animated animate__fadeInRight">
          {renderBreadcrumb()}
        </TransitionGroup>
      </ElBreadcrumb>
    )
  }
})
</script>

<style lang="scss" scoped>
$prefix-cls: #{$elNamespace}-breadcrumb;

.#{$prefix-cls} {
  font-size: 13px;

  :deep(.#{$prefix-cls}__separator) {
    display: flex;
    align-items: center;
    margin: 0 6px;
    font-size: 11px;
    color: var(--top-header-text-color);
    opacity: 0.3;
  }

  :deep(.#{$prefix-cls}__item) {
    display: flex;

    .#{$prefix-cls}__inner {
      display: flex;
      align-items: center;
      color: var(--top-header-text-color);
      opacity: 0.72;
      transition:
        color 0.2s ease,
        opacity 0.2s ease;

      &:hover {
        color: var(--el-color-primary);
        opacity: 1;
      }
    }
  }

  :deep(.#{$prefix-cls}__item):last-child {
    .#{$prefix-cls}__inner {
      display: flex;
      align-items: center;
      font-weight: 600;
      color: var(--top-header-text-color);
      cursor: default;
      opacity: 1;

      &:hover {
        color: var(--top-header-text-color);
      }
    }
  }

  // 当前页标识：品牌渐变圆点
  :deep(.bc-current) {
    display: inline-flex;
    align-items: center;
    gap: 7px;

    .bc-dot {
      flex-shrink: 0;
      width: 6px;
      height: 6px;
      background-image: var(--app-brand-gradient);
      border-radius: 50%;
      box-shadow: 0 0 0 3px rgba(var(--el-color-primary-rgb), 0.12);
    }
  }
}
</style>

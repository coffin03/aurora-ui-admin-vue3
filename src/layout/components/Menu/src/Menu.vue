<script lang="tsx">
import { PropType } from 'vue'
import { ElMenu, ElScrollbar } from 'element-plus'
import { useAppStore } from '@/store/modules/app'
import { usePermissionStore } from '@/store/modules/permission'
import { useRenderMenuItem } from './components/useRenderMenuItem'
import { isUrl } from '@/utils/is'
import { useDesign } from '@/hooks/web/useDesign'
import { LayoutType } from '@/types/layout'

const { getPrefixCls } = useDesign()

const prefixCls = getPrefixCls('menu')

export default defineComponent({
  // eslint-disable-next-line vue/no-reserved-component-names
  name: 'Menu',
  props: {
    menuSelect: {
      type: Function as PropType<(index: string) => void>,
      default: undefined
    }
  },
  setup(props) {
    const appStore = useAppStore()

    const layout = computed(() => appStore.getLayout)

    const { push, currentRoute } = useRouter()

    const permissionStore = usePermissionStore()

    const menuMode = computed((): 'vertical' | 'horizontal' => {
      // 竖
      const vertical: LayoutType[] = ['classic', 'topLeft', 'cutMenu']

      if (vertical.includes(unref(layout))) {
        return 'vertical'
      } else {
        return 'horizontal'
      }
    })

    const routers = computed(() =>
      unref(layout) === 'cutMenu' ? permissionStore.getMenuTabRouters : permissionStore.getRouters
    )

    const collapse = computed(() => appStore.getCollapse)

    const uniqueOpened = computed(() => appStore.getUniqueOpened)

    const activeMenu = computed(() => {
      const { meta, path } = unref(currentRoute)
      // if set path, the sidebar will highlight the path you set
      if (meta.activeMenu) {
        return meta.activeMenu as string
      }
      return path
    })

    const menuSelect = (index: string) => {
      if (props.menuSelect) {
        props.menuSelect(index)
      }
      // 自定义事件
      if (isUrl(index)) {
        window.open(index)
      } else {
        push(index)
      }
    }

    const renderMenuWrap = () => {
      if (unref(layout) === 'top') {
        return renderMenu()
      } else {
        return <ElScrollbar>{renderMenu()}</ElScrollbar>
      }
    }

    const renderMenu = () => {
      return (
        <ElMenu
          defaultActive={unref(activeMenu)}
          mode={unref(menuMode)}
          collapse={
            unref(layout) === 'top' || unref(layout) === 'cutMenu' ? false : unref(collapse)
          }
          uniqueOpened={unref(layout) === 'top' ? false : unref(uniqueOpened)}
          backgroundColor="var(--left-menu-bg-color)"
          textColor="var(--left-menu-text-color)"
          activeTextColor="var(--left-menu-text-active-color)"
          popperClass={
            unref(menuMode) === 'vertical'
              ? `${prefixCls}-popper--vertical`
              : `${prefixCls}-popper--horizontal`
          }
          onSelect={menuSelect}
        >
          {{
            default: () => {
              const { renderMenuItem } = useRenderMenuItem(unref(menuMode))
              return renderMenuItem(unref(routers))
            }
          }}
        </ElMenu>
      )
    }

    return () => (
      <div
        id={prefixCls}
        class={[
          `${prefixCls} ${prefixCls}__${unref(menuMode)}`,
          'h-[100%] overflow-hidden flex-col bg-[var(--left-menu-bg-color)]',
          {
            'w-[var(--left-menu-min-width)]': unref(collapse) && unref(layout) !== 'cutMenu',
            'w-[var(--left-menu-max-width)]': !unref(collapse) && unref(layout) !== 'cutMenu'
          }
        ]}
      >
        {renderMenuWrap()}
      </div>
    )
  }
})
</script>

<style lang="scss" scoped>
$prefix-cls: #{$namespace}-menu;

.#{$prefix-cls} {
  position: relative;
  transition: width var(--transition-time-02);

  :deep(.#{$elNamespace}-menu) {
    width: 100% !important;
    padding: 10px 0;
    border-right: none;
    background-color: transparent;

    .#{$elNamespace}-menu-item,
    .#{$elNamespace}-sub-menu__title {
      height: 44px;
      margin: 2px 10px;
      padding-right: 10px;
      font-size: 14px;
      line-height: 44px;
      color: var(--left-menu-text-color);
      border-radius: var(--app-radius-sm);
      transition:
        background-color var(--transition-time-02),
        color var(--transition-time-02);
    }

    // 鼠标悬停
    .#{$elNamespace}-sub-menu__title,
    .#{$elNamespace}-menu-item {
      &:hover {
        color: var(--el-text-color-primary) !important;
        background-color: var(--left-menu-bg-hover-color) !important;
      }
    }

    // 选中项
    .#{$elNamespace}-menu-item.is-active {
      position: relative;
      font-weight: 600;
      color: var(--left-menu-text-active-color) !important;
      background-color: var(--left-menu-bg-active-color) !important;

      &::before {
        position: absolute;
        top: 50%;
        left: 0;
        width: 3px;
        height: 18px;
        background-color: var(--el-color-primary);
        border-radius: 0 3px 3px 0;
        content: '';
        transform: translateY(-50%);
      }

      &:hover {
        background-color: var(--left-menu-bg-active-color) !important;
      }
    }

    // 展开状态下的父级菜单
    .#{$elNamespace}-sub-menu.is-active > .#{$elNamespace}-sub-menu__title {
      font-weight: 600;
      color: var(--left-menu-text-active-color) !important;
    }

    // 子菜单背景保持通透
    .#{$elNamespace}-menu {
      .#{$elNamespace}-sub-menu__title,
      .#{$elNamespace}-menu-item:not(.is-active) {
        background-color: transparent !important;
      }
    }
  }

  // 折叠时的最小宽度
  :deep(.#{$elNamespace}-menu--collapse) {
    width: var(--left-menu-min-width);

    > .#{$elNamespace}-menu-item,
    > .#{$elNamespace}-sub-menu > .#{$elNamespace}-sub-menu__title {
      display: flex;
      width: auto;
      margin: 2px 8px;
      padding: 0 !important;
      justify-content: center;
    }

    .#{$elNamespace}-sub-menu__icon-arrow {
      display: none;
    }

    & > .is-active,
    & > .is-active > .#{$elNamespace}-sub-menu__title {
      position: relative;
      background-color: var(--left-menu-collapse-bg-active-color) !important;
      color: #fff !important;

      &::before {
        display: none;
      }
    }
  }

  // 折叠动画的时候，就需要把文字给隐藏掉
  :deep(.horizontal-collapse-transition) {
    // transition: 0s width ease-in-out, 0s padding-left ease-in-out, 0s padding-right ease-in-out !important;
    .#{$prefix-cls}__title {
      display: none;
    }
  }

  // 垂直菜单
  &__vertical {
    :deep(.#{$elNamespace}-menu--vertical) {
      &:not(.#{$elNamespace}-menu--collapse) .#{$elNamespace}-sub-menu__title,
      .#{$elNamespace}-menu-item {
        padding-right: 0;
      }
    }
  }

  // 水平菜单
  &__horizontal {
    height: calc(var(--top-tool-height)) !important;

    :deep(.#{$elNamespace}-menu--horizontal) {
      height: calc(var(--top-tool-height));
      border-bottom: none;
      // 重新设置底部高亮颜色
      & > .#{$elNamespace}-sub-menu.is-active {
        .#{$elNamespace}-sub-menu__title {
          border-bottom-color: var(--el-color-primary) !important;
        }
      }

      .#{$elNamespace}-menu-item.is-active {
        position: relative;

        &::after {
          display: none !important;
        }
      }

      .#{$prefix-cls}__title {
        /* stylelint-disable-next-line */
        max-height: calc(var(--top-tool-height) - 2px) !important;
        /* stylelint-disable-next-line */
        line-height: calc(var(--top-tool-height) - 2px);
      }
    }
  }
}
</style>

<style lang="scss">
$prefix-cls: #{$namespace}-menu-popper;

.#{$prefix-cls}--vertical,
.#{$prefix-cls}--horizontal {
  // 设置选中时子标题的颜色
  .is-active {
    & > .el-sub-menu__title {
      color: var(--left-menu-text-active-color) !important;
    }
  }

  // 设置子菜单悬停的高亮和背景色
  .el-sub-menu__title,
  .el-menu-item {
    &:hover {
      color: var(--el-text-color-primary) !important;
      background-color: var(--left-menu-bg-hover-color) !important;
    }
  }

  // 设置选中时的高亮背景
  .el-menu-item.is-active {
    position: relative;
    color: var(--left-menu-text-active-color) !important;
    background-color: var(--left-menu-bg-active-color) !important;

    &:hover {
      background-color: var(--left-menu-bg-active-color) !important;
    }
  }
}
</style>

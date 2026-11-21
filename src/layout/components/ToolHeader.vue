<script lang="tsx">
import { defineComponent, computed } from 'vue'
import { Message } from '@/layout/components//Message'
import { Collapse } from '@/layout/components/Collapse'
import { UserInfo } from '@/layout/components/UserInfo'
import { Screenfull } from '@/layout/components/Screenfull'
import { Breadcrumb } from '@/layout/components/Breadcrumb'
import { SizeDropdown } from '@/layout/components/SizeDropdown'
import { LocaleDropdown } from '@/layout/components/LocaleDropdown'
import RouterSearch from '@/components/RouterSearch/index.vue'
import { useAppStore } from '@/store/modules/app'
import { useDesign } from '@/hooks/web/useDesign'

const { getPrefixCls, variables } = useDesign()

const prefixCls = getPrefixCls('tool-header')

const appStore = useAppStore()

// 面包屑
const breadcrumb = computed(() => appStore.getBreadcrumb)

// 折叠图标
const hamburger = computed(() => appStore.getHamburger)

// 全屏图标
const screenfull = computed(() => appStore.getScreenfull)

// 搜索图片
const search = computed(() => appStore.search)

// 尺寸图标
const size = computed(() => appStore.getSize)

// 布局
const layout = computed(() => appStore.getLayout)

// 多语言图标
const locale = computed(() => appStore.getLocale)

// 消息图标
const message = computed(() => appStore.getMessage)

export default defineComponent({
  name: 'ToolHeader',
  setup() {
    return () => (
      <div
        id={`${variables.namespace}-tool-header`}
        class={[
          prefixCls,
          'h-[var(--top-tool-height)] relative px-[var(--top-tool-p-x)] flex items-center'
        ]}
      >
        {layout.value !== 'top' ? (
          <div class="h-full flex items-center gap-1">
            {hamburger.value && layout.value !== 'cutMenu' ? (
              <div class="header-action">
                <Collapse color="var(--top-header-text-color)"></Collapse>
              </div>
            ) : undefined}
            {breadcrumb.value ? <Breadcrumb class="lt-md:hidden"></Breadcrumb> : undefined}
          </div>
        ) : undefined}
        <div class="h-full flex items-center gap-1 ml-auto">
          {screenfull.value ? (
            <div class="header-action">
              <Screenfull color="var(--top-header-text-color)"></Screenfull>
            </div>
          ) : undefined}
          {search.value ? (
            <div class="header-action header-action--search">
              <RouterSearch isModal={false} />
            </div>
          ) : undefined}
          {size.value ? (
            <div class="header-action">
              <SizeDropdown color="var(--top-header-text-color)"></SizeDropdown>
            </div>
          ) : undefined}
          {locale.value ? (
            <div class="header-action">
              <LocaleDropdown color="var(--top-header-text-color)"></LocaleDropdown>
            </div>
          ) : undefined}
          {message.value ? (
            <div class="header-action">
              <Message color="var(--top-header-text-color)"></Message>
            </div>
          ) : undefined}
          <div class="header-divider"></div>
          <UserInfo></UserInfo>
        </div>
      </div>
    )
  }
})
</script>

<style lang="scss">
$prefix-cls: #{$namespace}-tool-header;

.#{$prefix-cls} {
  transition: left var(--transition-time-02);

  .header-action {
    display: flex;
    align-items: center;
    justify-content: center;
    box-sizing: border-box;
    min-width: 32px;
    height: 32px;
    padding: 0 6px;
    line-height: 1;
    cursor: pointer;
    border-radius: 10px;
    transition:
      background-color 0.2s ease,
      transform 0.15s ease;

    // 统一内部元素的垂直居中，避免图标按基线对齐而出现高低差
    > * {
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100%;
      line-height: 1;
    }

    .el-dropdown {
      line-height: 1;
    }

    .el-badge {
      display: inline-flex;
      align-items: center;
    }

    &:hover {
      background-color: var(--top-header-hover-color);
    }

    &:active {
      transform: scale(0.92);
    }
  }

  .header-divider {
    flex-shrink: 0;
    width: 1px;
    height: 16px;
    margin: 0 8px 0 5px;
    background-color: var(--top-header-text-color);
    opacity: 0.16;
  }

  .header-action--search {
    padding: 0 2px;
  }
}
</style>

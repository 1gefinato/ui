<template>
  <nav :class="ui.wrapper">
    <ULinkCustom
      v-for="(link, index) of links"
      v-slot="{ isActive }"
      :key="index"
      v-bind="link"
      :class="[ui.base, ui.padding]"
      :active-class="ui.active"
      :inactive-class="ui.inactive"
      @click="link.click && link.click()"
      @keyup.enter="$event.target.blur()"
    >
      <slot name="avatar" :link="link">
        <UAvatar
          v-if="link.avatar"
          v-bind="{ size: ui.avatar.size, ...link.avatar }"
          :class="[ui.avatar.base]"
        />
      </slot>
      <slot name="icon" :link="link" :is-active="isActive">
        <UIcon
          v-if="link.icon"
          :name="link.icon"
          :class="[ui.icon.base, isActive ? ui.icon.active : ui.icon.inactive, link.iconClass]"
        />
      </slot>
      <slot :link="link">
        <span v-if="link.label" :class="ui.label">{{ link.label }}</span>
      </slot>
      <slot name="badge" :link="link" :is-active="isActive">
        <span v-if="link.badge" :class="[ui.badge.baseClass, isActive ? ui.badge.active : ui.badge.inactive]">
          {{ link.badge }}
        </span>
      </slot>
    </ULinkCustom>
  </nav>
</template>

<script lang="ts">
import { computed, defineComponent } from 'vue'
import type { PropType } from 'vue'
import type { RouteLocationNormalized } from 'vue-router'
import { defu } from 'defu'
import UIcon from '../elements/Icon.vue'
import UAvatar from '../elements/Avatar.vue'
import ULinkCustom from '../elements/LinkCustom.vue'
import type { Avatar as AvatarType } from '../../types/avatar'
import { useAppConfig } from '#imports'
// TODO: Remove
// @ts-expect-error
import appConfig from '#build/app.config'

// const appConfig = useAppConfig()

export default defineComponent({
  components: {
    UIcon,
    UAvatar,
    ULinkCustom
  },
  props: {
    links: {
      type: Array as PropType<{
      to?: RouteLocationNormalized | string
      exact?: boolean
      label: string
      icon?: string
      iconClass?: string
      avatar?: Partial<AvatarType>
      click?: Function
      badge?: string
    }[]>,
      default: () => []
    },
    ui: {
      type: Object as PropType<Partial<typeof appConfig.ui.verticalNavigation>>,
      default: () => appConfig.ui.verticalNavigation
    }
  },
  setup (props) {
    // TODO: Remove
    const appConfig = useAppConfig()

    const ui = computed<Partial<typeof appConfig.ui.verticalNavigation>>(() => defu({}, props.ui, appConfig.ui.verticalNavigation))

    return {
      // eslint-disable-next-line vue/no-dupe-keys
      ui
    }
  }
})
</script>

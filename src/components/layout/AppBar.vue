<script setup lang="ts">
import { useDark, useToggle } from '@vueuse/core'
const theme = useTheme()
const { drawer } = storeToRefs(useAppStore())
const route = useRoute()
const breadcrumbs = computed(() => {
  return route!.matched
    .slice(1)
    .filter(
      (item) =>
        item.meta && item.meta.title && !(item.meta?.breadcrumb === 'hidden'),
    )
    .map((r) => ({
      title: r.meta.title!,
      disabled:
        r.meta?.breadcrumb === 'disabled' || r.path === route.path || false,
      to: r.path,
    }))
})
const isDark = useDark({
  onChanged(dark: boolean) {
    theme.global.name.value = dark ? 'dark' : 'light'
    window.electronAPI.toggleDark(dark)
  },
})
const toggleDark = useToggle<true, false | null>(isDark)
</script>

<template>
  <v-app-bar flat>
    <v-app-bar-nav-icon @click="drawer = !drawer" />
    <v-breadcrumbs :items="breadcrumbs" />
    <v-spacer />
    <div id="app-bar" />
    <div class="mt-7 mr-2 d-flex">
      <!-- <v-btn
        icon
        href="https://github.com/neo-euni/neo-plan-ui-test.git"
        size="small"
        class="ml-2"
        target="_blank"
      >
        <v-icon size="30" icon="mdi-github" />
      </v-btn> -->
      <v-switch
        :model-value="isDark"
        color=""
        hide-details
        density="compact"
        inset
        false-icon="mdi-white-balance-sunny"
        true-icon="mdi-weather-night"
        style="opacity: 0.8"
        @update:model-value="toggleDark"
      />
    </div>
  </v-app-bar>
</template>

<style scoped>
:deep(.v-breadcrumbs-divider) {
  opacity: 0.5;
}
</style>

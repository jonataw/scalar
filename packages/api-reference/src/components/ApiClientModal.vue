<script setup lang="ts">
import { ApiClient, useApiClientStore } from '@scalar/api-client'
import { useMediaQuery } from '@vueuse/core'

import { type Spec } from '../types'
import { default as Sidebar } from './Sidebar.vue'

defineProps<{
  spec: Spec
  overloadShow?: boolean
  tabMode?: boolean
  activeTab?: string
  proxyUrl?: string
}>()

const { hideApiClient, state } = useApiClientStore()

const isMobile = useMediaQuery('(max-width: 1000px)')
</script>
<script lang="ts">
export { useApiClientStore } from '@scalar/api-client'
</script>
<template>
  <div
    v-if="state.showApiClient || overloadShow"
    class="api-client-drawer">
    <div class="scalar-api-client__overlay">
      <div class="scalar-api-client__container">
        <slot name="header" />
        <!-- <div class="scalar-api-client__navigation">
          <button
            class="scalar-api-client__close"
            type="button"
            @click="hideApiClient">
            <span>Back to Reference</span>
          </button>
        </div> -->
        <div class="scalar-api-client-height flex flex-row">
          <template v-if="tabMode">
            <template v-if="activeTab === 'sidebar'">
              <div class="t-doc__sidebar">
                <Sidebar
                  v-show="!isMobile"
                  :spec="spec" />
              </div>
            </template>
            <template v-else>
              <slot name="active-tab"></slot>
            </template>
          </template>
          <template v-else>
            <div class="t-doc__sidebar">
              <Sidebar
                v-show="!isMobile"
                :spec="spec" />
            </div>
          </template>
          <ApiClient
            :proxyUrl="proxyUrl"
            readOnly
            theme="none"
            @escapeKeyPress="hideApiClient" />
        </div>
      </div>
    </div>
  </div>
  <div
    v-if="state.showApiClient"
    class="api-client-drawer-exit"
    @click="hideApiClient"></div>
</template>

<style scoped>
.scalar-api-client__container .scalar-api-client {
  width: calc(100% - var(--refs-sidebar-width));
}
@media screen and (max-width: 1000px) {
  .scalar-api-client__container .scalar-api-client {
    width: 100%;
  }
}
.scalar-api-client__container {
  position: absolute;
  right: 0;
  left: 0;
  bottom: 0;
  top: 0;
  z-index: 9;
  border-radius: 0;
  box-shadow: none;
  opacity: 1;
  overflow: hidden;
  pointer-events: all;
  background: var(
    --theme-background-1,
    var(--default-theme-background-1)
  ) !important;
  border-radius: var(--theme-radius-lg, var(--default-theme-radius-lg));
  box-shadow: var(--theme-shadow-1, var(--default-theme-shadow-1));
  height: 100%;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.scalar-api-client__navigation {
  width: 100%;
  display: flex;
  align-items: center;
  padding: 11px 12px;
  height: var(--refs-header-height);
  background-color: var(
    --theme-background-1,
    var(--default-theme-background-1)
  );
  z-index: 10;
  position: sticky;
  border-bottom: 1px solid
    var(--theme-border-color, var(--default-theme-border-color));
  top: 0;
}

.scalar-api-client__close {
  appearance: none;
  border: none;
  outline: none;
  display: flex;
  align-items: center;
  background: transparent;
  font-size: var(--theme-small, var(--default-theme-small));
  color: var(--theme-color-1, var(--default-theme-color-1));
  font-weight: var(--theme-semibold, var(--default-theme-semibold));
}
.scalar-api-client__close:hover {
  cursor: pointer;
}
/*
TODO: Markup is missing
.scalar-api-client__close__icon {
  width: 28px;
  height: 28px;
  border-radius: var(--theme-radius-lg, var(--default-theme-radius-lg));
  margin-right: 12px;
  display: flex;
  justify-content: center;
  align-items: center;
  color: var(--theme-color-2, var(--default-theme-color-2));
}
.scalar-api-client__close__icon:hover {
  background: var(--theme-background-2, var(--default-theme-background-2));
}
.scalar-api-client__close__icon svg {
  width: 12px;
  height: 12px;
  transform: rotate(180deg);
} */
.api-client-drawer {
  background: var(--theme-background-1, var(--default-theme-background-1));
  height: calc(100% - 58px);
  width: calc(100% - 8px);
  border-radius: 12px;
  overflow: hidden;
  visibility: visible;
  position: fixed;
  bottom: 4px;
  left: 4px;
  z-index: 1001;
  opacity: 0;
  animation: apiclientfadein 0.35s forwards;
}
@keyframes apiclientfadein {
  from {
    transform: translate3d(0, 20px, 0) scale(0.985);
    opacity: 0;
  }
  to {
    transform: translate3d(0, 0, 0) scale(1);
    opacity: 1;
  }
}
.api-client-drawer-exit {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.44);
  transition: all 0.3s ease-in-out;
  z-index: 1000;
  cursor: pointer;
  animation: drawerexitfadein 0.35s forwards;
}
.api-client-drawer-exit:before {
  content: '\00d7';
  font-family: sans-serif;
  position: absolute;
  top: 0;
  right: 0;
  font-size: 30px;
  font-weight: 100;
  line-height: 50px;
  right: 12px;
  text-align: center;
  color: white;
  opacity: 0.6;
}
.api-client-drawer-exit:hover:before {
  opacity: 1;
}
@keyframes drawerexitfadein {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
.scalar-api-client-height {
  height: 100%;
}
.scalar-api-client-height .sidebar {
  flex: 1 1 0%;
  flex-grow: 1;
  flex-shrink: 1;
  flex-basis: 0%;
  height: 100%;
  display: flex;
  flex-direction: column;
  width: var(--refs-sidebar-width);
  max-width: var(--refs-sidebar-width);
  border-right: 1px solid
    var(
      --sidebar-border-color,
      var(
        --default-sidebar-border-color,
        var(--theme-border-color, var(--default-theme-border-color))
      )
    );
}
</style>

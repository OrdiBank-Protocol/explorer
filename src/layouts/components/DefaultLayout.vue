<template>
  <div class="bg-gray-100 dark:bg-[#1b1b1b]">
    <!-- sidebar -->
    <div
      class="w-64 fixed z-50 left-0 top-0 bottom-0 overflow-auto bg-base-100 dark:bg-[#1e1e1e]"
      :class="{ block: sidebarShow, 'hidden xl:!block': !sidebarShow }"
    >
      <div class="flex justify-between mt-1 pl-4 py-4 mb-1">
        <RouterLink to="/" class="flex items-center">
          <img class="w-10 h-10" src="../../assets/logo.svg" />
          <h1 class="flex-1 ml-3 text-2xl f-medium font-semibold dark:text-white">
            OrdiBank
          </h1>
        </RouterLink>
        <div
          class="pr-4 cursor-pointer xl:!hidden"
          @click="sidebarShow = false"
        >
          <Icon icon="mdi-close" class="text-2xl" />
        </div>
      </div>
      <div
        v-for="(item, index) of blockchain.computedChainMenu"
        :key="index"
      >
        <div
          v-if="isNavGroup(item)"
          :tabindex="index"
          class="collapse rounded-none"
          :class="{
            'collapse-arrow':index > 0 && item?.children?.length > 0,
            'collapse-open': index === 0 && sidebarOpen,
            'collapse-close': index === 0 && !sidebarOpen,
          }"
        >
          <div>
            <div
              v-if="item?.badgeContent"
              class="mr-6 badge badge-sm text-white dark:text-zinc-900 border-none"
              :class="item?.badgeClass"
            >
              {{ item?.badgeContent }}
            </div>
          </div>
          <div class="collapse-content px-0">            
            <div v-for="(el, key) of item?.children" class="menu bg-base-100 dark:bg-transparent w-full !p-0">
              <RouterLink
                v-if="isNavLink(el)"
                @click="sidebarShow = false"
                class="hover:bg-gray-100 dark:hover:bg-[#262525] cursor-pointer px-6 py-[10px] flex items-center"
                :class="{
                  'bg-gray-200 dark:bg-[#262525]': selected(route, el),
                }"
                :to="el.to"
              >
              
                <img
                  v-if="el?.icon?.image"
                  :src="el?.icon?.image"
                  class="w-6 h-6 rounded-full mr-3 ml-4 " :class="{
                  'border border-gray-300 bg-white': selected(route, el),
                }"
                />
                <!-- class="text-gray-500 dark:text-gray-300" -->
                <div
                  class="text-base capitalize bg-[linear-gradient(180deg,_rgba(0,_0,_0,_0.40)0%,_rgba(153,_153,_153,_0.40)100%)] dark:bg-[linear-gradient(180deg,_rgba(255,_255,_255,_0.40)0%,_rgba(153,_153,_153,_0.40)100%)] text-transparent bg-clip-text"
                  :class="{
                    '!text-gray-800 dark:!text-white': selected(route, el),
                  }"
                >
                  {{ item?.title === 'Favorite' ? el?.title : $t(el?.title) }}
                </div>
              </RouterLink>
            </div>
            <div v-if="index === 0 && dashboard.networkType === NetworkType.Testnet" class="menu bg-base-100 w-full !p-0">
              <RouterLink 
              class="hover:bg-gray-100 dark:hover:bg-[#373f59] rounded cursor-pointer px-3 py-2 flex items-center"
              :to="`/${blockchain.chainName}/faucet`">
                <Icon
                  icon="mdi:chevron-right"
                  class="mr-2 ml-3"
                  ></Icon>
                <div
                  class="text-base capitalize text-gray-500 dark:text-gray-300"
                >
                  Faucet
                </div>
                <div
                  class="badge badge-sm text-white border-none badge-error ml-auto" 
                >
                  New
                </div>
              </RouterLink>
            </div>
          </div>
        </div>

        <RouterLink
          v-if="isNavLink(item)"
          :to="item?.to"
          @click="sidebarShow = false"
          class="cursor-pointer rounded-lg px-4 flex items-center py-2 hover:bg-gray-100 dark:hover:bg-[#373f59]"
        >
          <Icon
            v-if="item?.icon?.icon"
            :icon="item?.icon?.icon"
            class="text-xl mr-2"
            :class="{
              'text-yellow-500': item?.title === 'Favorite',
              'text-blue-500': item?.title !== 'Favorite',
            }"
          />
          <img
            v-if="item?.icon?.image"
            :src="item?.icon?.image"
            class="w-6 h-6 rounded-full mr-3 border border-blue-100"
          />
          <div
            class="text-base capitalize flex-1 text-gray-700 dark:text-gray-200 whitespace-nowrap"
          >
            {{ item?.title }}
          </div>
          <div
            v-if="item?.badgeContent"
            class="badge badge-sm text-white border-none" 
            :class="item?.badgeClass"
          >
            {{ item?.badgeContent }}
          </div>
        </RouterLink>
        <div
          v-if="isNavTitle(item)"
          class="px-4 text-sm text-gray-400 pb-2 uppercase"
        >
          {{ item?.heading }}
        </div>
      </div>

    </div>
    <div class="xl:!ml-64 px-3 pt-4 min-h-screen">
      <!-- header -->
      <div
        class="flex items-center py-3 bg-base-100 dark:bg-[#211f1f] mb-4 rounded px-4 sticky top-0 z-10"
      >
        <div
          class="text-2xl pr-3 cursor-pointer xl:!hidden"
          @click="sidebarShow = true"
        >
          <Icon icon="mdi-menu" />
        </div>

        <!-- <ChainProfile /> -->
        <NavbarSearch class="!inline-block" />

        <div class="flex-1 w-0"></div>

        <!-- <NavSearchBar />-->
        <NavBarI18n class="hidden md:!inline-block" />
        <NavbarThemeSwitcher class="!inline-block" />
        <!-- <NavBarWallet /> -->
      </div>

      <!-- 👉 Pages -->
      <div style="min-height: calc(100vh - 180px);">
          <div v-if="behind" class="alert alert-error mb-4">
              <div class="flex gap-2">
                  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                      class="stroke-current flex-shrink-0 w-6 h-6">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                          d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                  </svg>
                  <span>{{ $t('pages.out_of_sync') }} {{ blocktime.format() }} ({{ blocktime.fromNow() }})</span>
              </div>
          </div>
        <RouterView v-slot="{ Component }">
          <Transition mode="out-in">
            <Component :is="Component" />
          </Transition>
        </RouterView>
      </div>

      <newFooter />
    </div>
  </div>
</template>

<script lang="ts" setup>
  import { Icon } from '@iconify/vue';
  import { computed, ref } from 'vue';

  // Components
  import newFooter from '@/layouts/components/NavFooter.vue';
  import NavbarThemeSwitcher from '@/layouts/components/NavbarThemeSwitcher.vue';
  import NavbarSearch from '@/layouts/components/NavbarSearch.vue';
  import ChainProfile from '@/layouts/components/ChainProfile.vue';

  import { NetworkType, useDashboard } from '@/stores/useDashboard';
  import { useBaseStore, useBlockchain } from '@/stores';

  import NavBarI18n from './NavBarI18n.vue';
  import NavBarWallet from './NavBarWallet.vue';
  import type { NavGroup, NavLink, NavSectionTitle, VerticalNavItems } from '../types';
  import dayjs from 'dayjs';
import { useRoute } from 'vue-router';

  const route = useRoute()
  console.log(" ===route====>", route)
  const dashboard = useDashboard();
  dashboard.initial();
  const blockchain = useBlockchain();
  blockchain.randomSetupEndpoint();
  const baseStore = useBaseStore();

  const current = ref(''); // the current chain
  const temp = ref('')
  blockchain.$subscribe((m, s) => {
    if(current.value ===s.chainName && temp.value != s.endpoint.address) {
      temp.value = s.endpoint.address
      blockchain.initial();
    }
    if (current.value != s.chainName) {
      current.value = s.chainName;
      blockchain.randomSetupEndpoint();
    }
  });

  const sidebarShow = ref(false);
  const sidebarOpen = ref(true);

  const changeOpen = (index: Number) => {
    if (index === 0) {
      sidebarOpen.value = !sidebarOpen.value;
    }
  };
  const showDiscord = window.location.host.search('ping.pub') > -1;

  function isNavGroup(nav: VerticalNavItems | any): nav is NavGroup {
    return (<NavGroup>nav).children !== undefined;
  }
  function isNavLink(nav: VerticalNavItems | any): nav is NavLink {
    return (<NavLink>nav).to !== undefined;
  }
  function isNavTitle(nav: VerticalNavItems | any): nav is NavSectionTitle {
    return (<NavSectionTitle>nav).heading !== undefined;
  }
  function selected(route: any, nav: NavLink) {
    // console.log('route ===', route)
    // console.log('nav ===', nav)
    const b = route.path === nav.to?.path || route.path.startsWith(nav.to?.path) && nav.title.indexOf('dashboard') === -1
    // const b = route.path === nav.to?.path || nav.title.indexOf('dashboard') === -1
    return b
  }
  const blocktime = computed(() => {
    return dayjs(baseStore.latest?.block?.header?.time)
  });

  const behind = computed(() => {
    const current = dayjs().subtract(10, 'minute')
    return blocktime.value.isBefore(current)
  });

  dayjs()
</script>


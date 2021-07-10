<template>
	<div :class="containerClass()" @click="onWrapperClick">
		<AppTopBar @menu-toggle="onMenuToggle" />

        <transition name="layout-sidebar">
            <div :class="sidebarClass()" @click="onSidebarClick" v-show="isSidebarVisible()">
                <div class="layout-logo">
                    <router-link to="/">
                        <img alt="Logo" src="/assets/layout/images/logo-white.svg" />
                    </router-link>
                </div>
                <AppMenu :model="menu" @menuitem-click="onMenuItemClick" />
            </div>
        </transition>

		<div class="layout-main">
			<router-view />
		</div>
		<AppFooter />
	</div>
</template>

<script>
import AppTopBar from './AppTopbar.vue';
import AppMenu from './AppMenu.vue';
import AppFooter from './AppFooter.vue';
import { useRoute } from 'vue-router'
import { onBeforeUpdate, watch, inject } from 'vue';
import { reactive, toRefs } from '@vue/reactivity';

export default {
    setup() {
        
        const toast = inject('toast');
        const route = useRoute();

        const state = reactive({
            staticMenuInactive: false,
            mobileMenuActive: false,
            menu : [
                {label: 'Dashboard', icon: 'pi pi-fw pi-home', to: '/'},
                {label: 'Empty Page', icon: 'pi pi-fw pi-bookmark', to: '/empty'},
                {
                    label: 'Menu Hierarchy', icon: 'pi pi-fw pi-search',
                    items: [
                        {
                            label: 'Submenu 1', icon: 'pi pi-fw pi-bookmark',
                            items: [
                                {
                                    label: 'Submenu 1.1', icon: 'pi pi-fw pi-bookmark',
                                    items: [
                                        {label: 'Submenu 1.1.1', icon: 'pi pi-fw pi-bookmark'},
                                        {label: 'Submenu 1.1.2', icon: 'pi pi-fw pi-bookmark'},
                                        {label: 'Submenu 1.1.3', icon: 'pi pi-fw pi-bookmark'},
                                    ]
                                },
                                {
                                    label: 'Submenu 1.2', icon: 'pi pi-fw pi-bookmark',
                                    items: [
                                        {label: 'Submenu 1.2.1', icon: 'pi pi-fw pi-bookmark'},
                                        {label: 'Submenu 1.2.2', icon: 'pi pi-fw pi-bookmark'}
                                    ]
                                },
                            ]
                        },
                        {
                            label: 'Submenu 2', icon: 'pi pi-fw pi-bookmark',
                            items: [
                                {
                                    label: 'Submenu 2.1', icon: 'pi pi-fw pi-bookmark',
                                    items: [
                                        {label: 'Submenu 2.1.1', icon: 'pi pi-fw pi-bookmark'},
                                        {label: 'Submenu 2.1.2', icon: 'pi pi-fw pi-bookmark'},
                                        {label: 'Submenu 2.1.3', icon: 'pi pi-fw pi-bookmark'},
                                    ]
                                },
                                {
                                    label: 'Submenu 2.2', icon: 'pi pi-fw pi-bookmark',
                                    items: [
                                        {label: 'Submenu 2.2.1', icon: 'pi pi-fw pi-bookmark'},
                                        {label: 'Submenu 2.2.2', icon: 'pi pi-fw pi-bookmark'}
                                    ]
                                }
                            ]
                        }
                    ]
                },
                {label: 'View PrimeVue', icon: 'pi pi-fw pi-globe', command: () => {window.open("https://primefaces.org/primevue/showcase/#/")}}
            ]
        });

        watch(route, () => {
            state.menuActive = false;
            toast.removeAllGroups();
        });

        const onWrapperClick = () => {
            if (!state.menuClick) {
                state.mobileMenuActive = false;
            }

            state.menuClick = false;
        };

        const onMenuToggle = (event) => {
            state.menuClick = true;

            if (isDesktop()) {
                state.staticMenuInactive = !state.staticMenuInactive;
            }
            else {
                state.mobileMenuActive = !state.mobileMenuActive;
            }

            event.preventDefault();
        };

        const onSidebarClick = () => {
            state.menuClick = true;
        };

        const onMenuItemClick = (event) => {
            if (event.item && !event.item.items) {
                state.mobileMenuActive = false;
            }
        };

        const addClass = (element, className) => {
            if (element.classList)
                element.classList.add(className);
            else
                element.className += ' ' + className;
        };

        const removeClass = (element, className) => {
            if (element.classList)
                element.classList.remove(className);
            else
                element.className = element.className.replace(new RegExp('(^|\\b)' + className.split(' ').join('|') + '(\\b|$)', 'gi'), ' ');
        };

        const isDesktop = () => {
            return window.innerWidth > 1024;
        };

        const isSidebarVisible = () => {
            if (isDesktop()) {
                return !state.staticMenuInactive;
            }
            else {
                return true;
            }
        };

        const containerClass = () => {
            return ['layout-wrapper', {
                'layout-static': true,
                'layout-static-sidebar-inactive': state.staticMenuInactive,
                'layout-mobile-sidebar-active': state.mobileMenuActive
            }];
        };

        const sidebarClass = () => {
            return ['layout-sidebar', 'layout-sidebar-dark'];
        };

        onBeforeUpdate(() => {
            if (state.mobileMenuActive)
                addClass(document.body, 'body-overflow-hidden');
            else
                removeClass(document.body, 'body-overflow-hidden');
        });

        return {
            ...toRefs(state),
            state,
            onWrapperClick,
            onMenuToggle,
            onSidebarClick,
            onMenuItemClick,
            addClass,
            removeClass,
            isDesktop,
            isSidebarVisible,
            containerClass,
            sidebarClass,
        };
    },
    components: {
        'AppTopBar': AppTopBar,
        'AppMenu': AppMenu,
        'AppFooter': AppFooter,
    }
}
</script>

<style lang="scss">
@import './App.scss';
</style>

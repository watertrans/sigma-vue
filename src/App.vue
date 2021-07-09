<template>
	<div :class="containerClass()" @click="onWrapperClick">
		<AppTopBar @menu-toggle="onMenuToggle" />

        <transition name="layout-sidebar">
            <div :class="sidebarClass()" @click="onSidebarClick" v-show="isSidebarVisible()">
                <div class="layout-logo">
                    <router-link to="/">
                        <img alt="Logo" :src="logo" />
                    </router-link>
                </div>

                <AppProfile />
                <AppMenu :model="menu" @menuitem-click="onMenuItemClick" />
            </div>
        </transition>

		<div class="layout-main">
			<router-view />
		</div>

		<AppConfig :layoutMode="layoutMode" :layoutColorMode="layoutColorMode" @layout-change="onLayoutChange" @layout-color-change="onLayoutColorChange"/>

		<AppFooter />
	</div>
</template>

<script>
import AppTopBar from './AppTopbar.vue';
import AppProfile from './AppProfile.vue';
import AppMenu from './AppMenu.vue';
import AppConfig from './AppConfig.vue';
import AppFooter from './AppFooter.vue';
import { useRoute } from 'vue-router'
import { onBeforeUpdate, watch, inject } from 'vue';
import { reactive, toRefs } from '@vue/reactivity';
import { usePrimeVue } from 'primevue/config';

export default {
    setup() {
        
        const primevue = usePrimeVue();
        const appState = inject('appState');
        const toast = inject('toast');
        const route = useRoute();

        const state = reactive({
            layoutMode: 'static',
            layoutColorMode: 'dark',
            staticMenuInactive: false,
            overlayMenuActive: false,
            mobileMenuActive: false,
            menu : [
                {label: 'Dashboard', icon: 'pi pi-fw pi-home', to: '/'},
				{
					label: 'UI Kit', icon: 'pi pi-fw pi-sitemap',
					items: [
						{label: 'Form Layout', icon: 'pi pi-fw pi-id-card', to: '/formlayout'},
						{label: 'Input', icon: 'pi pi-fw pi-check-square', to: '/input'},
                        {label: "Float Label", icon: "pi pi-fw pi-bookmark", to: "/floatlabel"},
                        {label: "Invalid State", icon: "pi pi-fw pi-exclamation-circle", to: "invalidstate"},
						{label: 'Button', icon: 'pi pi-fw pi-mobile', to: '/button'},
						{label: 'Table', icon: 'pi pi-fw pi-table', to: '/table'},
						{label: 'List', icon: 'pi pi-fw pi-list', to: '/list'},
						{label: 'Tree', icon: 'pi pi-fw pi-share-alt', to: '/tree'},
						{label: 'Panel', icon: 'pi pi-fw pi-tablet', to: '/panel'},
						{label: 'Overlay', icon: 'pi pi-fw pi-clone', to: '/overlay'},
						{label: 'Menu', icon: 'pi pi-fw pi-bars', to: '/menu'},
						{label: 'Message', icon: 'pi pi-fw pi-comment', to: '/messages'},
						{label: 'File', icon: 'pi pi-fw pi-file', to: '/file'},
						{label: 'Chart', icon: 'pi pi-fw pi-chart-bar', to: '/chart'},
						{label: 'Misc', icon: 'pi pi-fw pi-circle-off', to: '/misc'},
					]
				},
				{
					label: "Utilities", icon:'pi pi-fw pi-globe',
					items: [
						{label: 'Display', icon:'pi pi-fw pi-desktop', to:'/display'},
						{label: 'Elevation', icon:'pi pi-fw pi-external-link', to:'/elevation'},
						{label: 'Flexbox', icon:'pi pi-fw pi-directions', to:'/flexbox'},
						{label: 'Icons', icon:'pi pi-fw pi-search', to:'/icons'},
						{label: 'Grid System', icon:'pi pi-fw pi-th-large', to:'/grid'},
						{label: 'Spacing', icon:'pi pi-fw pi-arrow-right', to:'/spacing'},
						{label: 'Typography', icon:'pi pi-fw pi-align-center', to:'/typography'},
						{label: 'Text', icon:'pi pi-fw pi-pencil', to:'/text'},
					]
				},
				{
					label: 'Pages', icon: 'pi pi-fw pi-clone',
					items: [
						{label: 'Crud', icon: 'pi pi-fw pi-user-edit', to: '/crud'},
						{label: 'Calendar', icon: 'pi pi-fw pi-calendar-plus', to: '/calendar'},
						{label: 'Timeline', icon: 'pi pi-fw pi-calendar', to: '/timeline'},
						{label: 'Empty Page', icon: 'pi pi-fw pi-circle-off', to: '/empty'}
					]
				},
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
                {label: 'Documentation', icon: 'pi pi-fw pi-question', command: () => {window.location = "#/documentation"}},
                {label: 'View Source', icon: 'pi pi-fw pi-search', command: () => {window.location = "https://github.com/primefaces/sigma-vue"}}
            ]
        });

        watch(route, () => {
            state.menuActive = false;
            toast.removeAllGroups();
        });

        const onWrapperClick = () => {
            if (!state.menuClick) {
                state.overlayMenuActive = false;
                state.mobileMenuActive = false;
            }

            state.menuClick = false;
        };

        const onMenuToggle = (event) => {
            state.menuClick = true;

            if (isDesktop()) {
                if (state.layoutMode === 'overlay') {
					if(state.mobileMenuActive === true) {
						state.overlayMenuActive = true;
					}

                    state.overlayMenuActive = !state.overlayMenuActive;
					state.mobileMenuActive = false;
                }
                else if (state.layoutMode === 'static') {
                    state.staticMenuInactive = !state.staticMenuInactive;
                }
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
                state.overlayMenuActive = false;
                state.mobileMenuActive = false;
            }
        };

		const onLayoutChange = (layoutMode) => {
			state.layoutMode = layoutMode;
		};

		const onLayoutColorChange = (layoutColorMode) => {
			state.layoutColorMode = layoutColorMode;
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
                if (state.layoutMode === 'static')
                    return !state.staticMenuInactive;
                else if (state.layoutMode === 'overlay')
                    return state.overlayMenuActive;
                else
                    return true;
            }
            else {
                return true;
            }
        };

        const containerClass = () => {
            return ['layout-wrapper', {
                'layout-overlay': state.layoutMode === 'overlay',
                'layout-static': state.layoutMode === 'static',
                'layout-static-sidebar-inactive': state.staticMenuInactive && state.layoutMode === 'static',
                'layout-overlay-sidebar-active': state.overlayMenuActive && state.layoutMode === 'overlay',
                'layout-mobile-sidebar-active': state.mobileMenuActive,
                'p-input-filled': appState.inputStyle === 'filled',
                'p-ripple-disabled': primevue.config.ripple === false
            }];
        };

        const sidebarClass = () => {
            return ['layout-sidebar', {
                'layout-sidebar-dark': state.layoutColorMode === 'dark',
                'layout-sidebar-light': state.layoutColorMode === 'light'
            }];
        };

        const logo = () => {
                return (state.layoutColorMode === 'dark') ? "assets/layout/images/logo-white.svg" : "assets/layout/images/logo.svg";
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
            onLayoutChange,
            onLayoutColorChange,
            addClass,
            removeClass,
            isDesktop,
            isSidebarVisible,
            containerClass,
            sidebarClass,
            logo,
        };
    },
    components: {
        'AppTopBar': AppTopBar,
        'AppProfile': AppProfile,
        'AppMenu': AppMenu,
        'AppConfig': AppConfig,
        'AppFooter': AppFooter,
    }
}
</script>

<style lang="scss">
@import './App.scss';
</style>

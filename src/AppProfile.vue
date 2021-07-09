<template>
	<div class="layout-profile">
		<div>
			<img src="assets/layout/images/profile.png" alt="" />
		</div>
		<button class="p-link layout-profile-link" @click="onClick">
			<span class="username">Claire Williams</span>
			<i class="pi pi-fw pi-cog"></i>
		</button>
        <transition name="layout-submenu-wrapper">
            <ul v-show="expanded">
                <li><button class="p-link"><i class="pi pi-fw pi-user"></i><span>Account</span></button></li>
                <li><button class="p-link"><i class="pi pi-fw pi-inbox"></i><span>Notifications</span><span class="menuitem-badge">2</span></button></li>
                <li><button class="p-link"><i class="pi pi-fw pi-power-off"></i><span>Logout</span></button></li>
            </ul>
        </transition>
		
	</div>
</template>

<script>
import { reactive, toRefs } from '@vue/reactivity';
	export default {
		setup() {
			const state = reactive({
				expanded: false,
			});

			const onClick = (event) => {
				state.expanded = !state.expanded;
				event.preventDefault();
			};

			return {
				...toRefs(state),
				onClick
			}
		}
	}
</script>

<style scoped>

</style>
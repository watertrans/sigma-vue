<template>
	<div class="layout-topbar">
		<button class="p-link layout-menu-button" @click="onMenuToggle">
			<span class="pi pi-bars"></span>
		</button>
		<div class="layout-topbar-icons">
			<button class="p-link">
				<span class="layout-topbar-item-text">Settings</span>
				<span class="layout-topbar-icon pi pi-cog"></span>
			</button>
			<button class="p-link">
				<span class="layout-topbar-item-text">User</span>
				<span class="layout-topbar-icon pi pi-user"></span>
			</button>
		</div>
	</div>
</template>

<script>
export default {
	setup(props, context) {
		const onMenuToggle = (event) => {
            context.emit('menu-toggle', event);
        }

		return {
			onMenuToggle
		}
	}
}
</script>
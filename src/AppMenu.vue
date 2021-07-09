<template>
	<div class="layout-menu-container">
		<AppSubmenu :items="model" class="layout-menu" :root="true" @menuitem-click="onMenuItemClick" />
	</div>
</template>

<script>
import AppSubmenu from './AppSubmenu';

export default {
	setup(props, context) {
		const onMenuItemClick = (event) => {
            context.emit('menuitem-click', event);
        }

		return {
			onMenuItemClick,
		}
	},
	props: {
		model: Array
	},
	components: {
		'AppSubmenu': AppSubmenu
	}
}
</script>

<style scoped>

</style>
<template>
	<div id="layout-config" :class="containerClass()" ref="root">
		<a href="#" class="layout-config-button" id="layout-config-button" @click="toggleConfigurator">
			<i class="pi pi-cog"></i>
		</a>
		<a href="#" class="layout-config-close" @click="hideConfigurator">
			<i class="pi pi-times"></i>
		</a>

		<div class="layout-config-content">

			<h5 style="margin-top: 0">Input Style</h5>
			<div class="p-formgroup-inline">
				<div class="p-field-radiobutton">
					<RadioButton id="input_outlined" name="inputstyle" value="outlined" :modelValue="inputStyle()" @update:modelValue="changeInputStyle" />
					<label for="input_outlined">Outlined</label>
				</div>
				<div class="p-field-radiobutton">
					<RadioButton id="input_filled" name="inputstyle" value="filled" :modelValue="inputStyle()" @update:modelValue="changeInputStyle" />
					<label for="input_filled">Filled</label>
				</div>
			</div>

			<h5>Ripple Effect</h5>
			<InputSwitch :modelValue="rippleActive()" @update:modelValue="changeRipple" />

			<h5>Menu Type</h5>
			<div class="p-formgroup-inline">
				<div class="p-field-radiobutton">
					<RadioButton id="static" name="layoutMode" value="static" v-model="d_layoutMode" @change="changeLayout($event, 'static')" />
					<label for="static">Static</label>
				</div>
				<div class="p-field-radiobutton">
					<RadioButton id="overlay" name="layoutMode" value="overlay" v-model="d_layoutMode" @change="changeLayout($event, 'overlay')" />
					<label for="overlay">Overlay</label>
				</div>
			</div>

			<h5>Menu Color</h5>
			<div class="p-formgroup-inline">
				<div class="p-field-radiobutton">
					<RadioButton id="dark" name="layoutColorMode" value="dark" v-model="d_layoutColorMode" @change="changeLayoutColor($event, 'dark')" />
					<label for="dark">Dark</label>
				</div>
				<div class="p-field-radiobutton">
					<RadioButton id="light" name="layoutColorMode" value="light" v-model="d_layoutColorMode" @change="changeLayoutColor($event, 'light')" />
					<label for="light">Light</label>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import { reactive, toRefs } from '@vue/reactivity';
import { useRoute } from 'vue-router'
import { ref, watch, inject } from 'vue';
import { usePrimeVue } from 'primevue/config';

	export default {
		setup(props, context) {

			const root = ref(null);
			const primevue = usePrimeVue();
			const appState = inject('appState');
			const route = useRoute();

			const state = reactive({
				active: false,
				d_layoutMode: props.layoutMode,
				d_layoutColorMode: props.layoutColorMode,
				outsideClickListener: null,
			});

			watch(route, () => {
				if (state.active) {
					state.active = false;
					unbindOutsideClickListener();
				}
			});

			watch(() => props.layoutMode, (newValue) => {
				state.d_layoutMode = newValue;
			});

			watch(() => props.layoutColorMode, (newValue) => {
				state.d_layoutColorMode = newValue;
			});

			const toggleConfigurator = (event) => {
				state.active = !state.active;
				event.preventDefault();

				if (state.active)
					bindOutsideClickListener();
				else
					unbindOutsideClickListener();
			};

			const hideConfigurator = (event) => {
				state.active = false;
				unbindOutsideClickListener();
				event.preventDefault();
			};

			const changeInputStyle = (value) => {
				appState.inputStyle = value;
			};

			const changeRipple = (value) => {
				primevue.config.ripple = value;
			};

			const changeLayout = (event, layoutMode) => {
				context.emit('layout-change', layoutMode);
				event.preventDefault();
			};

			const changeLayoutColor = (event, layoutColor) => {
				context.emit('layout-color-change', layoutColor);
				event.preventDefault();
			};

			const bindOutsideClickListener = () => {
				if (!state.outsideClickListener) {
					state.outsideClickListener = (event) => {
						if (state.active && isOutsideClicked(event)) {
							state.active = false;
						}
					};
					document.addEventListener('click', state.outsideClickListener);
				}
			};

			const unbindOutsideClickListener = () => {
				if (state.outsideClickListener) {
					document.removeEventListener('click', state.outsideClickListener);
					state.outsideClickListener = null;
				}
			};

			const isOutsideClicked = (event) => {
				return !(root.value.isSameNode(event.target) || root.value.contains(event.target));
			};

			const containerClass = () => {
				return ['layout-config', {'layout-config-active': state.active}];
			};

			const rippleActive = () => {
				return primevue.config.ripple;
			};

			const inputStyle = () => {
				return appState.inputStyle;
			};

			return {
				...toRefs(state),
				toggleConfigurator,
				hideConfigurator,
				changeInputStyle,
				changeRipple,
				changeLayout,
				changeLayoutColor,
				bindOutsideClickListener,
				unbindOutsideClickListener,
				isOutsideClicked,
				containerClass,
				rippleActive,
				inputStyle,
				root,
			};
		},
		props: {
			layoutMode: {
				type: String,
				default: null
			},
			layoutColorMode: {
				type: String,
				default: null
			}
		},
	}
</script>

<template>
	<div
		v-if="isVisible"
		role="scrollbar"
		:aria-controls="ariaControls"
		:aria-valuenow="ariaValuenow"
		:aria-valuemin="ariaValuemin"
		:aria-valuemax="ariaValuemax"
		:aria-orientation="ariaOrientation"
		class="c-custom-scrollbar"
		:style="{
			'--scrollbar-handle-position': `${
				scrollbarData.handlePosition * 100
			}%`,
			'--scrollbar-handle-size': `${scrollbarData.handleSize * 100}%`,
		}"
		@mousedown="jumpToClickPoint"
	>
		<slot
			name="beforeRail"
			v-bind="{ scrollBy, scrollToStart, scrollToEnd }"
		></slot>
		<div ref="rail" class="c-custom-scrollbar__rail">
			<button
				ref="handle"
				class="c-custom-scrollbar__handle"
				:class="handleClass"
				:style="handleStyle"
				tabindex="-1"
				@mousedown="startDrag"
			></button>
		</div>
		<slot
			name="afterRail"
			v-bind="{ scrollBy, scrollToStart, scrollToEnd }"
		></slot>
	</div>
</template>

<script setup>
// https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/scrollbar_role#associated_wai-aria_roles_states_and_properties

const emit = defineEmits(['update:ariaValuenow']);
const props = defineProps({
	persistent: {
		type: Boolean,
		default: false,
	},
	// Aria and functionality
	ariaControls: {
		type: String,
		default: undefined,
	},
	ariaValuenow: {
		type: [String, Number],
		default: 0,
	},
	ariaValuemin: {
		type: [String, Number],
		default: 0,
	},
	ariaValuemax: {
		type: [String, Number],
		default: 100,
	},
	ariaOrientation: {
		type: String,
		default: 'vertical',
		validator: (value) => ['vertical', 'horizontal'].includes(value),
	},

	// Styling
	handleClass: [Object, Array, String],
	handleStyle: [Object, Array, String],
});

const scrollbarObserver = ref(null);
const targetObserver = ref(null);
const isBeingDragged = ref(false);

const rail = ref(null);
const handle = ref(null);
const scrollbarData = ref({
	railLength: 0,
	handlePosition: 0,
	handleSize: 1,
});
const targetData = ref({
	get canScroll() {
		return this.innerSize > this.outerSize;
	},
	scrolledAmount: 0,
	outerSize: 0,
	innerSize: 0,
});

const isVisible = computed(() => {
	return (props.ariaControls && targetData.value.canScroll) || props.persistent;
});

defineExpose({
	isVisible,
});

watch(targetData, () => {
	detectOwnSize();
});
watch(
	() => targetData.value.canScroll,
	(canScroll) => {
		if (canScroll) {
			initialize();
		}
	}
);

// Make ariaValuenow v-model compatible
const ariaValuenow = ref(props.ariaValuenow);
watch(
	() => props.ariaValuenow,
	(value) => (ariaValuenow.value = +value)
);
watch(ariaValuenow, (value) => emit('update:ariaValuenow', value));

// Track target element
const target = ref(null);
watch(
	target,
	(target, oldTarget) => {
		if (oldTarget) {
			targetObserver.value.unobserve(oldTarget);
			oldTarget.removeEventListener('scroll', handleScrollFromTarget);
		}
		if (target) {
			targetObserver.value.observe(target);
			target.addEventListener('scroll', handleScrollFromTarget);
		}
		// Run the handling function once to set initial values
		handleScrollFromTarget();
	},
	{ immediate: true }
);

const mountInitialization = (count = 0) => {
	if (props.ariaControls && document.getElementById(props.ariaControls)) {
		initialize();
	} else if (count < 5) {
		setTimeout(() => mountInitialization(count + 1), 50);
	}
};
onMounted(() => {
	mountInitialization();
});
onUnmounted(() => (target.value = null));

function initialize() {
	scrollbarObserver.value =
		scrollbarObserver.value || new ResizeObserver(detectOwnSize);
	targetObserver.value =
		targetObserver.value || new ResizeObserver(handleScrollFromTarget);
	target.value = document.getElementById(props.ariaControls);

	detectTargetSize();
}

function handleScrollFromTarget() {
	detectTargetSize();
}

function jumpToClickPoint(event) {
	if (isBeingDragged.value) return;

	if (target.value) {
		if (event.target.closest('.c-custom-scrollbar__rail')) {
			if (props.ariaOrientation === 'horizontal') {
				const { left, width } = rail.value.getBoundingClientRect();
				const { clientX } = event;
				target.value.scrollLeft =
					((clientX - left) / width) *
					(target.value.scrollWidth - target.value.clientWidth);
			} else {
				const { top, height } = rail.value.getBoundingClientRect();
				const { clientY } = event;
				target.value.scrollTop =
					((clientY - top) / height) *
					(target.value.scrollHeight - target.value.clientHeight);
			}
			nextTick(startDrag);
		}
	}
}

function startDrag() {
	if (isBeingDragged.value) return;

	isBeingDragged.value = true;
	window.addEventListener('mousemove', handleDrag);
	window.addEventListener('mouseup', stopDrag);
}
function handleDrag(event) {
	if (!isBeingDragged.value) return;

	if (target.value) {
		if (props.ariaOrientation === 'horizontal') {
			const { movementX } = event;
			const { width } = rail.value.getBoundingClientRect();
			const { scrollLeft, scrollWidth } = target.value;
			target.value.scrollLeft =
				scrollLeft + (movementX / width) * scrollWidth;
		} else {
			const { movementY } = event;
			const { height } = rail.value.getBoundingClientRect();
			const { scrollTop, scrollHeight } = target.value;
			target.value.scrollTop =
				scrollTop + (movementY / height) * scrollHeight;
		}
	}
}
function stopDrag() {
	isBeingDragged.value = false;
	window.removeEventListener('mousemove', handleDrag);
	window.removeEventListener('mouseup', stopDrag);
}

function detectTargetSize() {
	if (target.value) {
		if (props.ariaOrientation === 'horizontal') {
			const {
				scrollLeft,
				scrollWidth,
				clientWidth: width,
			} = target.value;
			if (scrollWidth !== width) {
				targetData.value.scrolledAmount =
					scrollLeft / (scrollWidth - width);
			} else {
				targetData.value.scrolledAmount = 0;
			}
			targetData.value.outerSize = width;
			targetData.value.innerSize = scrollWidth;
		} else {
			const {
				scrollTop,
				scrollHeight,
				clientHeight: height,
			} = target.value;
			targetData.value.scrolledAmount =
				scrollTop / (scrollHeight - height);
			targetData.value.outerSize = height;
			targetData.value.innerSize = scrollHeight;
		}
	} else {
		targetData.value.scrolledAmount = 0;
		targetData.value.outerSize = 0;
		targetData.value.innerSize = 0;
	}
	detectOwnSize();
}

function detectOwnSize() {
	if (rail.value && targetData.value?.outerSize) {
		if (props.ariaOrientation === 'horizontal') {
			const { width } = rail.value.getBoundingClientRect();
			let pixelSize =
				(targetData.value.outerSize / targetData.value.innerSize) *
				width;
			scrollbarData.value.railLength = width;
			scrollbarData.value.handleSize = pixelSize / width;

			scrollbarData.value.handlePosition =
				(targetData.value.scrolledAmount * (width - pixelSize)) /
				pixelSize;
			ariaValuenow.value =
				+props.ariaValuemin +
				targetData.value.scrolledAmount *
					(+props.ariaValuemax - props.ariaValuemin);

			nextTick(() => {
				pixelSize = handle.value.clientWidth;
				scrollbarData.value.handleSize = pixelSize / width;

				scrollbarData.value.handlePosition =
					(targetData.value.scrolledAmount * (width - pixelSize)) /
					pixelSize;
				ariaValuenow.value =
					+props.ariaValuemin +
					targetData.value.scrolledAmount *
						(+props.ariaValuemax - props.ariaValuemin);
			});
		} else {
			const { height } = rail.value.getBoundingClientRect();
			let pixelSize =
				(targetData.value.outerSize / targetData.value.innerSize) *
				height;
			scrollbarData.value.railLength = height;
			scrollbarData.value.handleSize = pixelSize / height;

			scrollbarData.value.handlePosition =
				(targetData.value.scrolledAmount * (height - pixelSize)) /
				pixelSize;
			ariaValuenow.value =
				+props.ariaValuemin +
				targetData.value.scrolledAmount *
					(+props.ariaValuemax - props.ariaValuemin);

			nextTick(() => {
				pixelSize = handle.value.clientHeight;
				scrollbarData.value.handleSize = pixelSize / height;

				scrollbarData.value.handlePosition =
					(targetData.value.scrolledAmount * (height - pixelSize)) /
					pixelSize;
				ariaValuenow.value =
					+props.ariaValuemin +
					targetData.value.scrolledAmount *
						(+props.ariaValuemax - props.ariaValuemin);
			});
		}
	} else {
		scrollbarData.value.railLength = 0;
		scrollbarData.value.handlePosition = 0;
		scrollbarData.value.handleSize = 1;
	}
}

function scrollBy(value) {
	if (target.value) {
		if (props.ariaOrientation === 'horizontal') {
			target.value.scrollBy(value, 0);
		} else {
			target.value.scrollBy(0, value);
		}
	}
}
function scrollToEnd() {
	if (target.value) {
		if (props.ariaOrientation === 'horizontal') {
			target.value.scrollBy(targetData.value.innerSize, 0);
		} else {
			target.value.scrollBy(0, targetData.value.innerSize);
		}
	}
}
function scrollToStart() {
	if (target.value) {
		if (props.ariaOrientation === 'horizontal') {
			target.value.scrollLeft = 0;
		} else {
			target.value.scrollTop = 0;
		}
	}
}
</script>

<style lang="postcss">
/* Rail defaults */
:where(.c-custom-scrollbar) {
	display: flex;
	flex-direction: column;
	width: 16px;
	height: 100%;
	background-color: lightgray;
	overflow: clip;
	user-select: none;
}
:where(.c-custom-scrollbar[aria-orientation='horizontal']) {
	width: 100%;
	height: 16px;
	flex-direction: row;
}
:where(.c-custom-scrollbar > *) {
	flex-shrink: 0;
	flex-grow: 0;
}
.c-custom-scrollbar > .c-custom-scrollbar__rail {
	position: relative;
	width: 100%;
	height: 100%;
	flex-shrink: 1;
}

/* Handle defaults */
:where(.c-custom-scrollbar__handle) {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	min-height: 16px;
	height: var(--scrollbar-handle-size, 50%);
	background-color: gray;
	transform: translateY(var(--scrollbar-handle-position, 0%));
}
:where(
		.c-custom-scrollbar[aria-orientation='horizontal']
			.c-custom-scrollbar__handle
	) {
	min-width: 16px;
	width: var(--scrollbar-handle-size, 50%);
	min-height: 0;
	height: 100%;
	transform: translateX(var(--scrollbar-handle-position, 0%));
}
</style>

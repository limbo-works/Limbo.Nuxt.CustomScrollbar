<template>
	<div
		id="__chokoladefabrikken"
		style="width: 100vw; height: 50vh; overflow-y: auto;"
	>
		<!-- <CustomScrollbar
			aria-controls="chokoladefabrikken"
			class="scrollbarVertical"
			handle-class="rebox"
			aria-orientation="vertical"
			:persistent="true"
		/> -->
		<div style="width:200vw;">

		</div>
		<CustomScrollbar
			ref="scrollbar"
			aria-controls="__chokoladefabrikken"
			class="scrollbarHorizontal"
			handle-class="rebox"
			aria-orientation="horizontal"
			:persistent="true"
		>
			<!-- <template #beforeRail="{ scrollBy, scrollToEnd, scrollToStart }"
				><button @click="scrollToStart" class="btn">
					<svg
						xmlns="http://www.w3.org/2000/svg"
						fill="none"
						viewBox="0 0 24 24"
						stroke-width="1.5"
						stroke="currentColor"
						class="icon"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							d="M10.5 19.5L3 12m0 0l7.5-7.5M3 12h18"
						/>
					</svg></button
			></template>
			<template #afterRail="{ scrollBy, scrollToEnd, scrollToStart }">
				<button @click="scrollToEnd" class="btn">
					<svg
						xmlns="http://www.w3.org/2000/svg"
						fill="none"
						viewBox="0 0 24 24"
						stroke-width="1.5"
						stroke="currentColor"
						class="icon"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							d="M13.5 4.5L21 12m0 0l-7.5 7.5M21 12H3"
						/>
					</svg>
				</button>
			</template> -->
		</CustomScrollbar>
	</div>
</template>

<script setup>
const scrollbar = ref(null);

onMounted(() => {
	console.log(scrollbar.value);

})
</script>

<style>
body {
	overflow: hidden; /* Hide scrollbars */
}
#__nuxt {
	height: 100vh;
	overflow-y: auto;
}

.icon {
	height: 12px;
	width: 12px;
}

.rebox {
	background: rgb(34, 193, 195);
	background: linear-gradient(
		26deg,
		rgb(219, 100, 20) 0%,
		rgb(30, 207, 139) 100%
	);
	border: none;
}
.test-wrapper {
	width: 200vw;
	height: 200vh;
}
.btn {
	border: none;
	display: grid;
	place-content: center center;
}
.scrollbarHorizontal {
	position: fixed;
	top: 0;
	left: 16px;
	width: 95%;
}
.scrollbarVertical {
	position: fixed;
	top: 0;
	left: 0px;
	height: 95%;
}
</style>

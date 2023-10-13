<template>
	<div class="test-wrapper">
		<CustomScrollbar aria-controls="__nuxt" class="scrollbar" handleClass="rebox" ><template #beforeRail>sdfsdfg</template> <template #afterRail>sdfsdfadsfasdfdssdfafsdasfddfsg</template></CustomScrollbar>
		<div class="rebox">

		</div>
	</div>
</template>

<style>
body {
	overflow: hidden; /* Hide scrollbars */
}
#__nuxt {
	height: 100vh;
	overflow-y: auto;
}
.rebox {
	background-color: red;
}
.test-wrapper {
	height: 200vh;
}
.scrollbar {
	position: fixed;
	top: 0;
	right: 50px;
	height: 80%;
}
</style>

<template>
	<div id="app">
		<header-component></header-component>
		<content-component></content-component>
	</div>
</template>

<script>
import HeaderComponent from './components/HeaderComponent.vue'
import ContentComponent from './components/ContentComponent.vue'

export default {
	name: 'App',
	components: {
		HeaderComponent,
		ContentComponent
	}
}
</script>

<style>

</style>

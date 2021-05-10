<template>
	<div>
		<Header></Header>
		<!-- Insert your own api-key here -->
		<GetMovies api_key="839ca25ec9551d4955ca89fcdaa33161"></GetMovies>
	</div>
</template>

<script>
import Header from '@/components/Header.vue';
import GetMovies from '@/components/GetMovies.vue';
export default {
	name: 'App',
	components: {
		Header,
		GetMovies,
	},
};
</script>

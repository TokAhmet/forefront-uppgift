<template>
	<div class="container">
		<button
			v-on:click="getMovieData"
			class="px-4 py-2 border border-transparent text-base font-medium rounded-md mt-10 shadow-sm text-white bg-gradient-to-r from-purple-600 to-indigo-600 hover:from-purple-700 hover:to-indigo-700 m-auto"
			:class="[showButton ? 'block' : 'hidden']"
		>
			Hämta filmer
		</button>
	</div>
	<main class="container" v-if="loading">
		<img :src="loadingImage" class="w-10 m-auto mt-10" />
	</main>

	<main class="container" v-else>
		<select name="Movies" class="form-select max-w-3xl m-auto mt-10 block w-full border p-3 rounded pr-3" :class="[showButton ? 'hidden' : 'block']">
			<option value="0">Show top 100 most popular movies</option>
			<option :value="item.id" v-for="(item, index) in title" :key="item.id">{{ index + 1 }}. {{ item }}</option>
		</select>
	</main>
</template>

<script>
export default {
	name: 'GetData',
	// send a api-key prop so that someone else can just insert their own api as a prop at App.vue
	props: ['api_key'],
	data() {
		return {
			// show button by defualt
			showButton: true,
			// loading gif only when clicked to load fetch from api
			loading: false,
			// get the gif image
			loadingImage: require('../assets/loading.gif'),
			// make an empty array to store the title values from the api we fetch
			title: [],
		};
	},

	methods: {
		// Fetch top 100 popular movies atm from themoviedb.org
		async fetchMovieData() {
			this.loading = true;
			// each page only contains 20 titles so we need to fetch at least 5 to get 100 titles in same promies
			const res = await Promise.all([
				fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${this.api_key}&page=1`),
				fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${this.api_key}&page=2`),
				fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${this.api_key}&page=3`),
				fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${this.api_key}&page=4`),
				fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${this.api_key}&page=5`),
			]) // We need to async them in order to return them as json object
				.then(async ([page1, page2, page3, page4, page5]) => {
					const getPage1Json = await page1.json();
					const getPage2Json = await page2.json();
					const getPage3Json = await page3.json();
					const getPage4Json = await page4.json();
					const getPage5Json = await page5.json();
					return [getPage1Json, getPage2Json, getPage3Json, getPage4Json, getPage5Json];
				}) // Add a finally timeout just to show that the loading gif acutally exists incase it would take some time to fetch the api
				.finally(() =>
					setTimeout(() => {
						this.loading = false;
					}, 2000)
				);

			return res;
		},

		// Click handler that runs the whole fetching process and loads the loading gif
		async getMovieData() {
			// hide the button when click and wait for loading gif to finish setTimout
			this.showButton = false;

			// fetch the data when setTimeout is done loading
			// and loop through each page reuslt array to store the value into the data title
			const data = await this.fetchMovieData();
			data.forEach((item) => {
				item.results.map((val) => {
					this.title.push(val.title);
				});
			});
		},
	},
};
</script>
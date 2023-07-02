<script lang="ts">
	import { createSearchStore, searchHandler } from '$lib/stores/search';
	import { onDestroy } from 'svelte';
	import type { PageData } from './$types';

	export let data: PageData;

	type Product = {
		title: string;
		images: string[];
		description: string;
		brand: string;
		category: string;
		searchTerms: string;
	};

	const searchProducts: Product[] = data.products.map((product: Product) => ({
		...product,
		searchTerms: `${product.title} ${product.description} ${product.brand} ${product.category}`
	}));

	const searchStore = createSearchStore(searchProducts);

	const unsubscribe = searchStore.subscribe((model) => searchHandler(model));

	onDestroy(() => {
		unsubscribe();
	});

	console.log($searchStore.data);
</script>

<div class="container py-5 mx-auto">
	<div class="mb-5">
		<h5 class="mb-2 text-lg font-bold">Search / Filter</h5>
		<input
			type="search"
			class="w-full input input-bordered input-primary"
			placeholder="Search..."
			bind:value={$searchStore.search}
		/>
	</div>
	<div class="grid grid-cols-1 gap-4 sm:grid-cols-3 xl:grid-cols-4">
		{#each $searchStore.filtered as product}
			<div class="flex-1 shadow-xl card bg-base-100">
				<figure>
					<img class="object-cover w-full h-40" src={product.images[0]} alt="Shoes" />
				</figure>
				<div class="card-body">
					<h2 class="card-title">{product.title}</h2>
					<p>product.description</p>
					<div class="badge badge-accent">{product.category}</div>
					<p>{product.brand}</p>
				</div>
			</div>
		{:else}
			<h1>Sorry, we couldn’t find any matches for "{$searchStore.search}"</h1>
		{/each}
	</div>
</div>

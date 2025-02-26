<svelte:options runes={true} />

<script lang="ts">
	import { Carousel, CarouselContent, CarouselItem } from '$lib/components/ui/carousel';
	import type { CarouselAPI } from '$lib/components/ui/carousel/context';
	import { cn } from '$lib/components/utils';
	import { fetchRandomMeal } from '$lib/mealdb/mealdbService';
	import type { CommonMeal } from '$lib/types';
	import CommonMealCard from '$lib/components/meal/CommonMealCard.svelte';

	const valueQueue: CommonMeal[] = [];
	const fetchNewValues = async () => {
		console.log('fetchNewValues / valueQueue.length', valueQueue.length);
		if (valueQueue.length > 7) return;
		const meal = await fetchRandomMeal();
		valueQueue.push(meal);
		await fetchNewValues();
	};
	const getNextValue = () => {
		return valueQueue.shift();
	};

	let api = $state<CarouselAPI>();
	let lastSelectedIndex = $state(0);
	let itemValues = $state<Array<CommonMeal>>([]);
	let scrolling = $state(false);
	$effect(() => {
		if (api) {
			api.on('pointerDown', () => {
				scrolling = true;
			});
			api.on('pointerUp', () => {
				scrolling = false;
			});
			api.on('select', () => {
				const nextMeal = getNextValue();
				if (nextMeal) {
					itemValues[lastSelectedIndex] = nextMeal;
				}
				lastSelectedIndex = api!.selectedScrollSnap();

				fetchNewValues();
			});
		}
	});
	$effect(() => {
		console.log('init effect');
		fetchNewValues().then(() => {
			console.log('fetchNewValues done');
			const meal1 = getNextValue();
			const meal2 = getNextValue();
			const meal3 = getNextValue();
			console.log('meals', meal1?.id, meal2?.id, meal3?.id);
			if (meal1 && meal2 && meal3) {
				itemValues = [meal1, meal2, meal3];
			}
		});
	});
</script>

{#if itemValues.length < 3}
	<p>Loading...</p>
{:else}
	<Carousel class="border" opts={{ loop: true }} setApi={(emblaApi) => (api = emblaApi)}>
		<CarouselContent>
			{#each itemValues as itemValue, index}
				<CarouselItem
					class={cn(
						'transition-opacity',
						index !== lastSelectedIndex && 'opacity-0',
						scrolling && 'opacity-20'
					)}
				>
					<CommonMealCard meal={itemValue} />
				</CarouselItem>
			{/each}
		</CarouselContent>
	</Carousel>
	<span>last {lastSelectedIndex}</span>
	<span>{JSON.stringify(itemValues.map((meal) => meal.title))}</span>
{/if}

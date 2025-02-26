<script lang="ts">
	import type { CommonMeal } from '$lib/types';
	import { Card, CardHeader, CardTitle, CardDescription } from '$lib/components/ui/card';
	import { CardContent } from '$lib/components/ui/card/index.js';
	import { MonitorPlay } from 'lucide-svelte';
	import { Badge } from '$lib/components/ui/badge';

	interface Props {
		meal: CommonMeal;
	}
	const { meal }: Props = $props();
</script>

<div class="h-screen p-2">
	<Card class="h-4/5">
		<CardHeader>
			<CardTitle>
				<a
					class="text-xl text-primary underline hover:text-primary/80"
					href={meal.source}
					target="_blank"
					rel="noopener noreferrer">{meal.title}</a
				>
			</CardTitle>
			<CardDescription>{meal.category} / {meal.area}</CardDescription>
		</CardHeader>
		<CardContent class="space-y-3">
			{#if meal.youtube}
				<a href={meal.youtube} target="_blank" rel="noopener noreferrer">
					<MonitorPlay class="size-8 text-red-600" />
				</a>
			{/if}
			<img class="h-auto max-h-80 object-contain" src={meal.thumbnail} alt={meal.title} />
			<ul class="flex gap-1">
				{#each meal.tags as tag}
					<li><Badge variant="secondary">{tag}</Badge></li>
				{/each}
			</ul>
		</CardContent>
	</Card>
</div>

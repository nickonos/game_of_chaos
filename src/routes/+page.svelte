<script lang="ts">
	import { storedElements } from '../lib/index';
	import { createVirtualizer } from '@tanstack/svelte-virtual';

	let virtualListEl: HTMLDivElement;

	$: rowVirtualizer = createVirtualizer<HTMLDivElement, HTMLDivElement>({
		count: 10000,
		getScrollElement: () => virtualListEl,
		estimateSize: () => 40,
		overscan: 5,
	});

	$: columnVirtualizer = createVirtualizer<HTMLDivElement, HTMLDivElement>({
		horizontal: true,
		count: 10000,
		getScrollElement: () => virtualListEl,
		estimateSize: () => 40,
		overscan: 5
	});
</script>

<div class="no-scrollbar h-screen w-screen overflow-auto bg-[#010409]" bind:this={virtualListEl}>
	<div
		style="position: relative; height: {$rowVirtualizer.getTotalSize()}px; width: {$columnVirtualizer.getTotalSize()}px;"
	>
		{#each $rowVirtualizer.getVirtualItems() as row (row.index)}
			{#each $columnVirtualizer.getVirtualItems() as col (col.index)}
				{@const storedRow: boolean[] | undefined = $storedElements[row.index]}
				{@const isClicked = storedRow ? storedRow[col.index] : false}
				<button
					class="no-button absolute top-0 left-0 rounded-lg {isClicked ? "bg-blue-700" : "bg-slate-950"}"
					style="width: {col.size}px; height: {row.size}px; transform: translateX({col.start}px) translateY({row.start}px);"
					onclick={() => {
						if (!storedRow) {
							$storedElements[row.index] = [];
						}
						$storedElements[row.index][col.index] = !$storedElements[row.index][col.index];
					}}
					aria-label="row: {row.index}, col: {col.index}"
					data-row={row.index}
					data-col={col.index}
				></button>
			{/each}
		{/each}
	</div>
</div>


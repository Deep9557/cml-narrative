<!--
 /src/lib/components/activities/drag-and-drop/Bank.svelte
 Bank.svelte
 cml-narrative
 
 Created by Ian Thompson on January 16th 2023
 icthomp@g.clemson.edu
 
 https://idealab.sites.clemson.edu
 
--->

<script>
	import {dndzone} from 'svelte-dnd-action';
	import {flip} from 'svelte/animate';
	const flipDurationMs = 200;
	
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

	export let items = [];
	export let type;
    export let id;

    let dropTargetStyle = {
        "outline": "#e2e8f0 dashed 2px",
        "border-radius": "0.375rem",
        "background": type == "bank" ? "#d1d5db" : ""
        // "outline-color": ""
    }
	
	function handleSort(e) {
		items = e.detail.items;

        const {items:newItems} = e.detail;

        if (e.type == "finalize") {
            dispatch("itemDropped", {
                id: id,
                items: newItems
            })
        }

	}
</script>
<section use:dndzone={{items, flipDurationMs, type, dropTargetStyle}} on:consider={handleSort} on:finalize={handleSort} class="flex min-h-16 h-full outline-black">
	{#each items as item(item.id)}
		<div  animate:flip={{duration:flipDurationMs}} class="p-5 bg-white m-3 rounded-md border shadow-md max-h-44 ">
            <div class="flex flex-col items-center space-y-3 ">
                <img src={item.img} alt="" class="h-24">
                <p class="text-xl text-gray-800">{item.title}</p>
            </div>
		</div>
	{/each}
</section>
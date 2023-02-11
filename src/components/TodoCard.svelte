<script lang="ts">
	import { createEventDispatcher } from "svelte";

	export let name: string;
	export let isDone: boolean;
	export let uuid: string;

	const dispatch = createEventDispatcher();
</script>

<div class="todo">
	<input
		type="checkbox"
		on:input={() => dispatch("toggleChecked", { uuid })}
		checked={isDone}
	/>
	<span>{name}</span>
</div>

<style>
	.todo {
		border: 1px solid gray;
		padding: 0.5rem 1rem;
		border-radius: 0.5rem;
		min-width: 8rem;
	}
</style>

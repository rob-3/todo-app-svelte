<script lang="ts">
	import TodoCard from "../components/TodoCard.svelte";
	import { v4 as uuidv4 } from "uuid";
	import { crossfade, fly } from "svelte/transition";
	import { flip } from "svelte/animate";
	import { counter } from "../stores/store";

	const [send, receive] = crossfade({
		duration: (d) => Math.sqrt(d * 200),
		fallback: (node) => fly(node, { y: 200 }),
	});

	interface Todo {
		name: string;
		isDone: boolean;
		uuid: string;
	}

	let todos: Todo[] = [];

	let input = "";

	const addTodo = () => {
		todos = [...todos, { name: input, isDone: false, uuid: uuidv4() }];
		input = "";
	};

	const onToggleChange = (event: any) => {
		const i = todos.findIndex((t) => t.uuid === event.detail.uuid);
		const oldTodo = todos[i];
		const newTodo = { ...oldTodo, isDone: !oldTodo.isDone };
		todos[i] = newTodo;
	};

	$: finishedTodos = todos.filter((todo) => todo.isDone);
	$: unfinishedTodos = todos.filter((todo) => !todo.isDone);
</script>

<div class="container">
	<h1>Todo App {$counter}</h1>
	<input type="text" bind:value={input} />
	<button on:click={addTodo}>Add Todo</button>
	<button on:click={() => $counter = $counter + 1}>Increment counter</button>
	<div class="two-column">
		<div class="todos">
			{#each unfinishedTodos as { name, isDone, uuid } (uuid)}
				<div
					in:receive={{ key: uuid }}
					out:send={{ key: uuid }}
					animate:flip={{ duration: 200 }}
				>
					<TodoCard
						{name}
						{isDone}
						{uuid}
						on:toggleChecked={onToggleChange}
					/>
				</div>
			{/each}
		</div>
		<div class="todos">
			{#each finishedTodos as { name, isDone, uuid } (uuid)}
				<div
					in:receive={{ key: uuid }}
					out:send={{ key: uuid }}
					animate:flip={{ duration: 200 }}
				>
					<TodoCard
						{name}
						{isDone}
						{uuid}
						on:toggleChecked={onToggleChange}
					/>
				</div>
			{/each}
		</div>
	</div>
</div>

<style>
	.two-column {
		display: flex;
		width: 100%;
		justify-content: space-evenly;
	}
	.container {
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	input[type="text"] {
		margin-bottom: 1rem;
	}
	.todos {
		margin-top: 1rem;
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
	}
</style>

<script>
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';
	export let name;
	export let task;
	let todos = [];
	let isAdding = false;
	onMount(async () => {
			await loadTodos()
	})
	
	async function loadTodos(){
		return fetch("http://localhost:3000/todos?filter=%7B%22order%22%3A%5B%22id%20DESC%22%5D%7D").then(response =>{
			return response.json();
		}).then(function(object) {
			todos = object
		});
	}

	function addTask(){
		if(task && task.length > 0){
			let body = {
					"title": task
				}
			fetch('http://localhost:3000/todos', {
				method: 'POST',
				headers: {
					"Content-Type": "application/json"
				},
				body: JSON.stringify(body)
			}).then(response =>{
				console.log(response)
				loadTodos()
			}).catch(error=>{
				console.log(error)
			})
		}
	}

	function handleAdd () {
		if(isAdding){
			addTask();
		}else{
			isAdding = true;
		}
	}

</script>

<main>
	<h1>TODO<br>APP</h1>
	<div class="wrapper">
		<div class="form-add">
			<input bind:value="{task}" class="{isAdding ? 'input-add active' : 'input-add'}" type="text" />
			<button on:click={handleAdd} class="btn-add"><span class="{isAdding ? 'toggle-icon-active toggle-icon' : 'toggle-icon'}"></span></button>
		</div>
		<ul class="list">
			{#each todos as todo}
				<li transition:fade>{todo.title}</li>
			{:else}
				<p>Cargando...</p>
			{/each}
		</ul>
	</div>
</main>

<style>
	:root{
		--orange : #ff3e00;
		--border: 2px;
	}
	main {
		text-align: center;
		padding: 1em;
		margin: 0 auto;
	}

	h1 {
		color: var(--orange);
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}
	.wrapper{
		width:90%;
		padding: 10px 20px;
		margin:auto;
	}

	.form-add{
		display: flex;
		justify-content:center;
		align-items:center;
	}
	.input-add{
		appearance: none;
		-webkit-appearance: none;
		outline: none;
		padding: 0px;
		width: 0px;
		border: 0px;
		height: 50px;
		border-radius: 25px;
		top:0;
		/* -webkit-transition: width 1s ease-in-out;
		-moz-transition: width 1s ease-in-out;
		-o-transition: width 1s ease-in-out; */
		transition: width 1s , padding 1s ;
	}

	.active{
		padding: 20px;
		width: calc(100% - 60px);
		border: var(--border) solid var(--orange);
	}
	
	.btn-add {
		width: 50px;
		height: 50px;
		margin-left: 10px;
		border: var(--border) solid var(--orange);
		background-color: transparent;
		border-radius: 100%;
		position: relative;
		box-shadow: 0px 3px 3px rgba(0, 0, 0, 0.3);
	}

	.toggle-icon, .toggle-icon::before, .toggle-icon::after {
		content:"";
		width:30px;
		height: 2px;
		position: absolute;
		background-color: var(--orange);
		margin-left: -15px;
		border-top: 1px solid var(--orange);
		border-radius: 2px;
		cursor: pointer;
		transition: 0.4s ease;
	}

	.toggle-icon::before{
		margin-top: calc(-5px - 2px);
	}
	
	.toggle-icon::after{
		margin-top: 5px
	}

	.toggle-icon-active{
		background-color:transparent;
		width:0;
		border: none;
	}

	.toggle-icon-active::before{
		/* transform: rotate(90deg); */
		margin:0;
	}
	.toggle-icon-active::after{
		margin:0;
		transform: rotate(90deg);
	}
	

	.list {
		list-style: none;
		padding:0;
		width:100%;
		margin: auto
	}

	.list li{
		border: var(--border) solid var(--orange);
		padding: 20px;
		margin: 10px 0px;
		border-radius: 15px;
	}

	
	
	@media (min-width: 640px) {
		main {
			max-width: none;
		}

		.wrapper{
			width:50%;
			margin:auto;
		}
	}
</style>
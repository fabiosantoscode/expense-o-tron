<script>
	import Upload from "./Upload.svelte"

	let expenses = [
		{}
	]

	const validate = expense =>
		Boolean(expense.description && expense.date && expense.time)

	let validExpenses = []
	$: validExpenses = expenses.filter(validate)

	const onAdd = () => {
		expenses = [...expenses, {}]
	}

	const onChangeFile = expense => event => {
		expense.file = event.currentTarget.files[0]
		expenses = expenses
	}

	const onDelete = expense => () => {
		expenses = expenses.filter(e => e !== expense)
	}
</script>

<main>
	<h1>Expense-o-tron</h1>
	{#each expenses as expense}
		<div class="expense" class:invalid={!validate(expense)}>
			<input bind:value={expense.description} />
			<input type="date" bind:value={expense.date} />
			<input type="time" bind:value={expense.time} />
			<label class="upload">
				Upload receipt
				<input hidden type="file" on:change={onChangeFile(expense)} />
			</label>
			<button class="delete" on:click={onDelete(expense)}><span>&times;</span></button>
		</div>
	{/each}

	<button on:click={onAdd}>Add expense</button>

	<div class="download-container">
		{#if expenses.every(validate)}
			<Upload expenses={validExpenses} />
		{:else if expenses.length > 0}
			<p class="some-invalid">
				Some expenses are <span class="invalid inline-invalid">invalid</span>.<br/>
				fix them to continue
			</p>
		{/if}
	</div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: fit-content;
		margin: 0 auto;
		color: #333;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	.expense {
		display: flex;
		align-items: center;
		padding: 0.3em 0.5em;
		margin: 1em;
	}

	.expense > * {
		margin: 0;
		align-self: stretch;
	}

	.expense > * + * {
		margin-left: 0.33em;
	}

	.upload {
    display: flex;
    align-items: center;
		padding: 0 1em;
		color: rgb(48, 127, 201);
		font-weight: bold;
		text-decoration: underline;
		cursor: pointer;
	}

	.delete {
		border: 0 none;
		background: transparent;
		font-weight: bold;
		line-height: 0.8;
		font-size: 1.2em;
		padding: 0 0.5em;
		color: #ff3e00;
		cursor: pointer;
		transition: transform 100ms linear;
		transform: scale(1.2)
	}

	.delete:hover, .delete:active, .delete:focus {
		transform: scale(1.5)
	}

	.download-container {
		margin-top: 1em;
	}

	.invalid {
		border: 2px dashed #ec9477;
		border-radius: 0.5em;
	}

	.inline-invalid {
		padding-left: 0.25em;
		padding-right: 0.25em;
		border-radius: 0.35em;
	}

	.some-invalid {
		opacity: 0.8;
	}
</style>
<script>
	import Upload from "./Upload.svelte"

	let expenses = [
		{}
	]

	const validate = expense =>
		Boolean(expense.description && expense.date && expense.time)

	let expensesWithValidation = []
	let validExpenses = []
	$: expensesWithValidation = expenses.map(expense => ({
		expense,
		isValid: validate(expense)
	}))
	$: validExpenses = expenses.filter(validate)


	const onAdd = () => {
		expenses = [...expenses, {}]
	}

	const onChangeFile = expense => event => {
		expense.file = event.currentTarget.files[0]
		expenses = expenses
	}
</script>

<main>
	<h1>Expense-o-tron</h1>
	{#each expenses as expense}
		<div class="expense" class:invalid={!validate(expense)}>
			<p hidden>{JSON.stringify(expense)}</p>
			<input bind:value={expense.description} />
			<input type="date" bind:value={expense.date} />
			<input type="time" bind:value={expense.time} />
			<label class="upload">
				Upload receipt
				<input hidden type="file" on:change={onChangeFile(expense)} />
			</label>
		</div>
	{/each}

	<button on:click={onAdd}>Add expense</button>
	<Upload expenses={validExpenses} />
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 800px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	.expense > * {
		margin: 0;
	}

	.expense > * + * {
		margin-left: 0.33em;
	}

	.expense {
		padding: 0.2em;
		margin: 1em;
	}

	.expense.invalid {
		border: 2px dashed #ff6e3d;
		border-radius: 0.5em;
	}

	.upload {
		display: inline-block;
		height: 2em;
		padding: 0 1em;
		color: rgb(48, 127, 201);
		font-weight: bold;
		text-decoration: underline;
		cursor: pointer;
	}
</style>
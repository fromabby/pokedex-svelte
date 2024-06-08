<script lang="ts">
	let username = ''
	let password = ''

	$: submitted = false
	$: usernameValid = true
	$: passwordValid = true

	const handleSubmit = () => {
		if (username && password) {
			submitted = true
		}
	}
</script>

<main>
	<h1>Login</h1>
	<form>
		<input
			bind:value={username}
			placeholder="Enter username"
			class={!usernameValid ? 'error' : ''}
			required
			on:blur={() => {
				if (username.length < 6 || username.length > 12) {
					usernameValid = false
				} else {
					usernameValid = true
				}
			}}
		/>
		{#if !usernameValid}
			<p class="error">Invalid username</p>
		{/if}
		<input
			type="password"
			bind:value={password}
			placeholder="Enter password"
			class={!passwordValid ? 'error' : ''}
			required
			on:blur={(e) => {
				if (password.length < 6 || password.length > 12) {
					passwordValid = false
				} else {
					passwordValid = true
				}
			}}
		/>
		{#if !passwordValid}
			<p class="error">Invalid password</p>
		{/if}
		<button type="submit" on:click|preventDefault={handleSubmit}>Submit</button>
	</form>
	{#if submitted}
		<p>Welcome {username}</p>
	{/if}
</main>

<style>
	main {
		height: calc(100vh - 75px);
		width: 100%;
		max-width: 768px;
		margin: auto;
		text-align: center;
		padding: 1rem;
	}

	input {
		padding: 0.25rem;
		border: 1px solid #ccc;
	}

	input.error {
		border: 1px solid red;
		color: black;
	}

	button {
		padding: 0.25rem 0.5rem;
		border-radius: 7px;
		background: transparent;
		border: 1px solid #ccc;
		cursor: pointer;
		min-width: 100px;
	}

	form {
		width: 300px;
		margin: 1rem auto;
		border: 1px solid #ccc;
		padding: 1rem;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 0.5rem;
	}

	.error {
		color: red;
		font-size: 12px;
	}
</style>

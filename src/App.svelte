<script>
	import TailwindCss from "./Tailwindcss.svelte";
	let var_n = 0,
		var_m = 0;
	let org = "";
	let data = [];
	let org_error = false,
		github_error = false,
		loading = false;
	// let forker_data;
	const formSubmit = async e => {
		e.preventDefault();
		loading = true;
		org_error = false;
		github_error = false;
		const url = `https://api.github.com/search/repositories?q=user:${org}&sort=forks&per_page=${var_n}&page=1`;
		let response = await fetch(url).catch(() => {
			github_error = true;
		});
		if (!response.ok) {
			org_error = true;
			data = [];
			loading = false;
			return;
		}
		let data1 = await response.json();
		data1 = data1.items;
		data = data1;
		loading = false;
	};

	const forkersView = async (name, i) => {
		loading = true;
		const url = `https://api.github.com/repos/${org}/${name}/forks?sort=oldest`;
		let response = await fetch(url);
		let data1 = await response.json();
		data[i] = { ...data[i], forker_data: data1.slice(0, var_m) };
		loading = false;
	};
</script>

<TailwindCss />

<main class="w-100 bg-gray-100 min-h-screen">
	<h1
		class="bg-blue-300 text-center py-7 text-xl font-bold sticky top-0 shadow-md w-full"
	>
		Github Explorer
	</h1>
	<div class="w-100 mx-auto mt-16 px-10 text-center">
		<form on:submit={formSubmit}>
			<label for="org">Organisation Name</label>
			<input
				type="text"
				name="org"
				bind:value={org}
				class="border-2 border-black rounded-md p-1"
			/>
			<label for="var_n">N (Top Repositories)</label>
			<input
				type="number"
				name="var_n"
				bind:value={var_n}
				class="border-2 border-black rounded-md p-1"
			/>
			<label for="var_m">M (Oldest Forks)</label>
			<input
				type="number"
				name="var_m"
				bind:value={var_m}
				class="border-2 border-black rounded-md p-1"
			/>
			<button
				type="sumbit"
				class="py-2 px-3 my-4 bg-gray-400 text-white border-2 border-black rounded-md focus:bg-gray-500 hover:bg-gray-500"
				>Submit</button
			>
		</form>
		{#if github_error}
			<p>Can't connect to Github API</p>
		{/if}
		{#if org_error}
			<p>No such Organisation</p>
		{/if}
	</div>
	<div class="w-100 text-center mx-auto mt-8">
		{#if loading}
			<div class="spin" />
		{/if}
	</div>
	{#if data && data[0]}
		<div class="w-16 mx-auto">
			<a href={data[0].owner.html_url} target="_blank">
				<img src={data[0].owner.avatar_url} alt={org} class="w-16" />
			</a>
		</div>
		<ul class="w-100 mx-auto  my-3">
			{#each data as { name, full_name, html_url, description, forks, forker_data }, i}
				<li class="my-8 border-b-4">
					<div class="text-center">
						<h2 class="text-lg font-medium">{i + 1}. {name}</h2>
						<a
							href={html_url}
							target="_blank"
							class="text-blue-400 hover:text-blue-500"
							>{full_name}</a
						>
						<p>{description}</p>
						<p><span class="font-medium">Forks:</span> {forks}</p>
						<p>
							<span class="font-medium">Forkers:</span><button
								on:click={forkersView(name, i)}
								class="px-2 py-1 bg-gray-500 text-white mx-4 rounded-sm"
								>View</button
							>
						</p>
						<ul>
							{#if forker_data}
								{#each forker_data as { owner: { login, html_url } }}
									<li>
										<a
											href={html_url}
											target="_blank"
											class="text-blue-400 hover:text-blue-500"
											>{login}</a
										>
									</li>
								{/each}
							{/if}
						</ul>
					</div>
				</li>
			{/each}
		</ul>
	{/if}
</main>

<style>
	@keyframes spinner {
		0% {
			transform: translate3d(-50%, -50%, 0) rotate(0deg);
		}
		100% {
			transform: translate3d(-50%, -50%, 0) rotate(360deg);
		}
	}
	.spin {
		position: relative;
	}
	.spin::before {
		animation: 1.5s linear infinite spinner;
		animation-play-state: inherit;
		border: solid 5px #cfd0d1;
		border-bottom-color: #1c87c9;
		border-radius: 50%;
		content: "";
		height: 40px;
		width: 40px;
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate3d(-50%, -50%, 0);
		will-change: transform;
	}
</style>

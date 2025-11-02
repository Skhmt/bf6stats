<script lang="ts">
	import Chart from "./lib/Chart.svelte";

	let hp = $state("100"); // 100 for mp, 180 for br/redsec, 60 for hc, 140 for gauntlet
	let ads = $state(false);
	let mv = $state(true);
	let charttype = $state("ttk"); // ttk, stk, dps

	let ar = $state(true);
	let lmg = $state(false);
	let smg = $state(false);
	let carbine = $state(false);
	let pistol = $state(false);
	let dmr = $state(false);

	let options = $derived({
		hp: parseInt(hp),
		ads,
		mv,
		charttype,
		ar,
		lmg,
		smg,
		carbine,
		pistol,
		dmr,
	});
</script>

<main class="flex flex-col h-screen">
	<div class="flex w-full flex-col lg:flex-row cursor-default navbar">
		<div class="ml-5 mr-5">
			<h1>BF6 Stats, 1.1.1.0 (28Oct25)</h1>
		</div>
		<div>
			<ul
				class="menu menu-horizontal lg:menu-horizontal bg-base-200 rounded-box"
			>
				<li>
					<label class="input input-xs lg:input-md">
						<span class="label">Chart</span>
						<select
							class="select select-xs lg:select-sm"
							bind:value={charttype}
						>
							<option value="ttk">TTK</option>
							<option value="stk">STK</option>
							<option value="dps">DPS</option>
						</select>
					</label>
				</li>
				<li>
					<label class="input input-xs lg:input-md">
						<span class="label">HP</span>
						<select
							class="select select-xs lg:select-sm"
							bind:value={hp}
						>
							<option value="60">60 (HC)</option>
							<option value="100">100 (MP)</option>
							<option value="140">140 (Ga)</option>
							<option value="180">180 (BR)</option>
						</select>
					</label>
				</li>
				<li>
					<label class="input input-xs lg:input-md">
						<span class="label">Add ADS time?</span>
						<input
							type="checkbox"
							class="checkbox checkbox-xs lg:checkbox-md checkbox-primary"
							bind:checked={ads}
						/>
					</label>
				</li>
				<li>
					<label class="input input-xs lg:input-md">
						<span class="label">Add velocity?</span>
						<input
							type="checkbox"
							class="checkbox checkbox-xs lg:checkbox-md checkbox-primary"
							bind:checked={mv}
						/>
					</label>
				</li>
				<li>
					<details class="dropdown">
						<summary class="btn btn-xs lg:btn-md btn-primary"
							>Weapon types</summary
						>
						<ul
							class="menu dropdown-content dropdown-content bg-base-100 rounded-box z-1 w-52 p-2 shadow-sm"
						>
							<li>
								<label class="label">
									<input
										type="checkbox"
										bind:checked={ar}
										class="checkbox"
									/>
									<span class="label">Assault Rifle</span>
								</label>
							</li>
							<li>
								<label class="label">
									<input
										type="checkbox"
										bind:checked={carbine}
										class="checkbox"
									/>
									<span class="label">Carbine</span>
								</label>
							</li>
							<li>
								<label class="label">
									<input
										type="checkbox"
										bind:checked={lmg}
										class="checkbox"
									/>
									<span class="label">LMG</span>
								</label>
							</li>
							<li>
								<label class="label">
									<input
										type="checkbox"
										bind:checked={smg}
										class="checkbox"
									/>
									<span class="label">SMG</span>
								</label>
							</li>
							<li>
								<label class="label">
									<input
										type="checkbox"
										bind:checked={dmr}
										class="checkbox"
									/>
									<span class="label">DMR</span>
								</label>
							</li>
							<li>
								<label class="label">
									<input
										type="checkbox"
										bind:checked={pistol}
										class="checkbox"
									/>
									<span class="label">Pistol</span>
								</label>
							</li>
						</ul>
					</details>
				</li>
			</ul>
		</div>
	</div>

	<div class="flex-grow w-full h-full mt-0">
		<Chart filters={options} />
	</div>
</main>

<style>
</style>

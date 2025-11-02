<script lang="ts">
	import Plotly from "plotly.js-dist-min";
	import rawData from "../assets/data.json";
	import lineColors from "../assets/linecolors.json";

	let { filters = $bindable() } = $props();

	let div: HTMLDivElement; // reference to the chart div
	let windowWidth = $state(window.innerWidth);
	let colorIndex = 0;
	const colorList: Array<ColorStyle> = generateColorList();

	// these *must* exactly correspond with the "range" keys in the rawData
	const distances = ["5m", "10m", "20m", "35m", "50m", "70m", "80m"];

	type Filter = {
		hp: number;
		ads: boolean;
		mv: boolean;
		charttype: string;
		ar: boolean;
		lmg: boolean;
		smg: boolean;
		carbine: boolean;
		pistol: boolean;
		dmr: boolean;
	};

	type Weapon = {
		weapon: string;
		type: string;
		ads: string;
		rpm: string;
		"5m": string;
		"10m": string;
		"20m": string;
		"35m": string;
		"50m": string;
		"70m": string;
		"80m": string;
		[key: string]: any;
	};

	type ColorStyle = {
		color: string;
		dash: string;
	};

	const plotlyLayout: Partial<Plotly.Layout> = {
		// height: 900,
		showlegend: true,
		plot_bgcolor: "black", // Dark background inside the plot
		paper_bgcolor: "black", // Dark background outside the plot
		font: {
			color: "white", // Adjust text color for better visibility
		},
		xaxis: {
			title: {
				text: "Distance (m)",
				standoff: 10,
			},
			gridcolor: "#333",
		},
		yaxis: {
			title: {
				text: "Time (ms)",
			},
			gridcolor: "#222",
		},
		legend: {
			orientation: "v",
		},
	};

	const plotlyOptions = {
		scrollZoom: true,
		displayModeBar: false,
		responsive: true,
	};

	$effect(() => {
		// console.log(filters);
		console.log(windowWidth);
		if (filters && rawData && windowWidth)
			renderChart(
				filters as Filter,
				rawData as Array<Weapon>,
				plotlyLayout,
				plotlyOptions,
				div,
				distances,
			);
	});

	// window.addEventListener("resize", function () {
	// 	if (filters && rawData && div)
	// 		renderChart(
	// 			filters as Filter,
	// 			rawData as Array<Weapon>,
	// 			plotlyLayout,
	// 			plotlyOptions,
	// 			div,
	// 			distances,
	// 		);
	// });

	/* 
		Example rawData
		[
			{
				"weapon": "AK4D",
				"type": "Assault Rifle", // Carbine, DMR, LMG, Pistol, SMG
				"ads": "250",
				"rpm": "514",
				"5m": "33.4",
				"10m": "33.4",
				"20m": "33.4",
				"35m": "25",
				"50m": "25",
				"70m": "25"
			},
			...
		]

		Example filter object
		{
			"hp": 200,
			"ads": false,
			"mv": false,
			"charttype": "ttk",
			"ar": true,
			"lmg": true,
			"smg": true,
			"carbine": true,
			"pistol": true,
			"dmr": true
		}

		Example plotly data
		[
			{
				x: [1, 2, 3, 4],
				y: [10, 15, 13, 17],
				type: 'scatter',
				name: 'thing 1',
			},
			...
		]
	 */

	function renderChart(
		filter: Filter,
		data: Array<Weapon>,
		layout: Partial<Plotly.Layout>,
		options: Object,
		element: HTMLDivElement,
		dists: Array<string>,
	) {
		colorIndex = 0;

		// filter the data to only include the weapon types selected by the user
		const filteredData = data.filter((item: any) => {
			return (
				(item.type === "Assault Rifle" && filter.ar) ||
				(item.type === "Carbine" && filter.carbine) ||
				(item.type === "DMR" && filter.dmr) ||
				(item.type === "LMG" && filter.lmg) ||
				(item.type === "Pistol" && filter.pistol) ||
				(item.type === "SMG" && filter.smg)
			);
		});

		const numDists: Array<number> = dists.map((d) =>
			parseInt(d.replace("m", "")),
		);

		// create the chart data
		const chartData: Array<Object> = [];
		for (let w of filteredData) {
			const y = [];
			for (let d of dists) {
				const damage = parseFloat(w[d]);
				const rpm = parseInt(w.rpm);
				const dist = parseInt(d.replace("m", ""));

				if (filter.charttype === "dps") {
					y.push(getDPS(damage, rpm));
				} else {
					// do STK calcs
					const stk = getSTK(damage, filter.hp);
					if (filter.charttype === "ttk") {
						const ads = filter.ads ? parseInt(w.ads) : 0;
						const traveltime =
							filter.mv && w.mv
								? getTravelTime(parseInt(w.mv), dist)
								: 0;
						let ttk = getTTK(rpm, stk);
						y.push(ttk + ads + traveltime);
					} else {
						y.push(stk);
					}
				}
			}

			let shortType = "";
			if (w.type === "Assault Rifle") shortType = "AR";
			else if (w.type === "Carbine") shortType = "Crbn";
			else if (w.type === "DMR") shortType = "DMR";
			else if (w.type === "LMG") shortType = "LMG";
			else if (w.type === "Pistol") shortType = "Pstl";
			else if (w.type === "SMG") shortType = "SMG";

			chartData.push({
				name: `[${shortType}] ${w.weapon}`,
				type: "scatter",
				mode: "lines+markers",
				x: numDists,
				y: y,
				line: {
					color: colorList[colorIndex].color,
					dash: colorList[colorIndex].dash,
				},
			});
			colorIndex++;
		}

		// axis labels
		if (filter.charttype === "dps") {
			layout!.yaxis!.title!.text = "Damage Per Second";
		} else if (filter.charttype === "stk") {
			layout!.yaxis!.title!.text = "Shots";
		} else {
			// TTK
			layout!.yaxis!.title!.text = "Time (mlliseconds)";
		}

		console.log(layout!.legend!.orientation);
		layout!.legend!.orientation = windowWidth < 850 ? "h" : "v";

		// @ts-ignore
		new Plotly.newPlot(element, chartData, layout, options);
	}

	function getDPS(damage: number, rpm: number) {
		return Math.ceil((damage * rpm) / 60);
	}

	function getSTK(damage: number, health: number) {
		return Math.ceil(health / damage);
	}

	function getTTK(rpm: number, stk: number) {
		// 60000 ms in a minute
		return Math.ceil((60000 * (stk - 1)) / rpm);
	}

	// assumes zero wind resistance, in milliseconds
	function getTravelTime(mv: number, dist: number) {
		// 1 sec / "mv" meters * "dist" meters * 1000 ms / 1 sec
		return (1 / mv) * dist * 1000;
	}

	function generateColorList() {
		const tempList = [];
		for (let c of lineColors) {
			tempList.push({
				color: c,
				dash: "solid",
			});
		}
		for (let c of lineColors) {
			tempList.push({
				color: c,
				dash: "dot",
			});
		}
		for (let c of lineColors) {
			tempList.push({
				color: c,
				dash: "dashdot",
			});
		}
		return tempList;
	}
</script>

<svelte:window bind:innerWidth={windowWidth} />
<div class="h-full">
	<div class="h-full" bind:this={div}></div>
</div>

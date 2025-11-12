# bf6 stats

## assumptions

- no air resistance for bullet velocity (this might be wrong)

## todo

- get support benefit for ADS with LMGs
- damage per magazine?
- headshot damage
- extend from 0 to 10m, 75m to 100m
- allow selection of each individual weapon, not just groups

## get data

data .json file should be in the format:

```js
[
	{
			"weapon": "AK4D",
			"type": "Assault Rifle", // Carbine, DMR, LMG, Pistol, SMG
			"ads": "250", // aim down sight - in milliseconds
			"rpm": "514", // rounds per minute - the firing rate
			"mv": "1000", // muzzle velocity (optional) - in meters per second
			"9.5m": "33.4", // damage at 5 meters
			"21.5m": "33.4",
			"36.5m": "33.4",
			"55m": "25",
			"75m": "25",
			"80m": "25"
		},
		//...
]
```

## scripts

develop: `npm run dev`

build for dist: `npm run build`

## license

MIT - do what you want
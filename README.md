# bf6 stats

## assumptions

- no air resistance
- damage falloff is in discrete steps, not gradually
- there are no other damage falloff distances besides 5m
- only two damage locations: head and everything else

## todo

- get support benefit for ADS with LMGs
- support resizing to phone width, specifically the menu settings
- damage per magazine?
- headshot damage

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
			"5m": "33.4", // damage at 5 meters
			"10m": "33.4",
			"20m": "33.4",
			"35m": "25",
			"50m": "25",
			"70m": "25"
		},
		//...
]
```

## scripts

develop: `npm run dev`

build for dist: `npm run build`
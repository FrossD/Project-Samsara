# Regional Generation Engine
An asset for [[The Skywards Pursuit]]; an engine for generation of regions using given constraining variables and probability tables.

## Engine Requirements
A sort of guide on what the engine needs to be able to do. 

- The engine will ask for a series of constraints with which it is bound by when generating a hypothetical region. These constraints are;
	- **Selected Rolling Tables**: the engine will constrain generated biomes to only those within selected tables or '**Biome Classes**'. 
		- Number of Biome Class Tables: `n`
		
	- **Table Forcing**: the ability to demand that the engine rolls on a particular **Biome Class Table** a certain number of times.
		- Biome Class Table Forced Roll Number (the number of times a give forced table will be rolled): `t1`, `t2`, `t3`... up to the number of forced tables.
		  
	- **Biome Number**: the number of biomes needed for the region. This number should allow an element of tolerance to it, in which coming within +/- 1 or 2 biomes of the biome number is acceptable.
		- Biome Number: `bn
		  `
	- **Regional Tier**: the average of all biome's tiers should come within +/- 0.5 of the target regional tier.
		- Range: 1 ≤ `rt` ≤ 5
		  
	- **Regional Size**: The sum of all biome's sizes must not exceed the regional size. If a biome would force the total biome size to exceed the regional size, the biome is rolled again. The Regional Size will have a tolerance range of +/- 1-2.
		- Regional Sizes (`rs`);
		  - Small: 5 (3-7)
		  - Medium: 10 (8-12)
		  - Large: 15 (13-17)
		  - Colossal: 20 (18-22)
		  - Custom: c

- Preferably, the engine would be able to read the values it operates with from a spreadsheet, as well as be able to accept new biomes added to that spreadsheet. This will allow for modifications and expansions in the future.

## Rolling Mechanism
How the engine rolls for biomes.

- When the engine wants to roll, it begins first with the **Class Roll**. 
	- This roll begins first by ascertaining what Biome Class Tables are to be rolled on from the given pool. If `n` tables are selected, there is a 1/`n` chance of a table being selected. 
	- If there are **forced tables**, the engine begins first with rolling on every forced table, `t` number of times. When this is satisfied, it will then roll normally for the remaining tables. If a table is a **forced table**, it will NOT be rolled on again in the second rolling stage.

- When a table is selected;
	- The engine will then roll on the **Biome Weight, Large Pool** to determine whether a **Standard** or **Special** biome will be chosen. Standard biomes have a **60%** chance of being chosen, while Special biomes have a **40%** chance of being chosen. These Standard and Special pools of biomes are known as the **Small Pools**.

- When a small pool is selected;
	- The engine will then perform a roll in the **Biome Weight, Small Pool**. Rolls in the Small Pool determine which biome the engine will select. Each biome has a given % chance of selection by the engine.

- When a biome is selected;
	- The engine will perform a final roll on the **Biome Size Propensity** table. This table has % values that illustrate the chance of a biome generating at a certain size. Sizes are expressed as values from 1 to 5.

- When the size is selected;
	- The selected biome, their Tier and their Size are then written to a table. When the first biome is selected, the engine will then begin begin keeping track of the variables **Average Biome Tier** (`abt`) and **Total Biome Size** (`tbs`).

## Working Within Constraints
As the engine rolls, it keeps track of the **Tier** and **Size** of the biomes it has selected, as well as the number of biomes it has selected. Because of this - and the constraints highlighted above - there are various scenarios in which meeting a parameter is grounds for the engine to **stop**, and 'print' the final biome list generated.

When the total number of requested biomes (biome number `bn`) is reached, this is arguably the highest binding variable that determines when the engine should stop, and so we will operate with it. If seven biomes are requested, then the engine should **prefer** to stop running once seven biomes have been selected.

In instances where selecting a certain biome would make the Total Biome Size `tbs` exceed the requested regional size `rs`, the last biome picked is rerolled.

If, however the `tbs` reaches the `rs` without reaching the requested `bn`, or within an acceptable range of it, the **entire rolling process should begin again**, as reaching the final `bn` or within range of it is a product of all prior biomes selected by the engine and not just the final one.

Similarly, if the `avt` falls under or overshoots the acceptable range of the `rt`, the entire rolling process should once again begin anew.
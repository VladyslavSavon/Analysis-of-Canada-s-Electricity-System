# Analysis of Canada`s Electricity System using PostgreSQL and Power BI 

This analysis leverages free data from [the Open Government Portal of Canada](https://open.canada.ca/data/en/dataset/2cdf43fc-d4aa-4604-9f21-29777d955810). The dataset, titled "Electricity Generation and Capacity in Canada 2005-2016," provides insights into the country's electricity generation and capacity over this period.

The repository documents the process and results of analyzing Canada's electricity system, focusing specifically on generation and capacity metrics.

This project marks my first attempt at analyzing open data using PostgreSQL and Power BI, providing a hands-on introduction to these powerful tools.

## TERMS AND DEFINITIONS

Power Capacity

Power capacity refers to the maximum amount of electricity a generator can produce when operating at full capacity. It is measured in Megawatts (MW).

Power Generation

Power generation is the process of producing electrical energy from primary energy sources. The output is measured in Gigawatt hours (GW/h), representing the total energy generated over time.

Source types:

Hydro - Hydroelectric power plants

Wind - Wind power plants

Biomass - Biomass power plants

Solar - Solar power plants

Nuclear - Nuclear power plants

Coal - Coal power plants

Natural Gas - Natural gas power plants

Oil and Diesel - Oil power plants (fuel oil and diesel)

Regions of Canada: Province of British Columbia, Province of Alberta, Province of Saskatchewan, Province of Manitoba, Province of Ontario, Province of Quebec, Province of New Brunswick, Province of Nova Scotia, Province of Prince Edward Island, Province of Newfoundland and Labrador, Northwest Territories, Yukon Territory, Nunavut Territory.

## Description

### 1. DATA ANALYSIS IN POSTGRESQL

Initially, I aim to determine the power capacity and power generation for the most recent year in the dataset, 2016.

```
SELECT *
FROM electricity_capacity_table
WHERE year = '2016'
```
AND
```
SELECT *
FROM electricity_generation_table
WHERE year = '2016'
```

Next, I will calculate the clean energy generation ratio for 2016, broken down by region. This analysis provides insights into the progress of Canada's green transition program at the regional level.

Using a Common Table Expression (CTE), I will divide the amount of clean energy generation by the total energy generation to compute the ratio.

```
WITH clean_energy_sum_gen AS
(
			SELECT 
				region
				, SUM (data) AS clean_energy_sum
			FROM electricity_generation_table
			WHERE year = '2016' and source != 'Coal' and source != 'Natural Gas' and source != 'Oil and Diesel' 
			GROUP BY region
)

, energy_sum_2016 AS 
(
			SELECT 
				region
				, SUM (data) AS energy_sum
			FROM electricity_generation_table
			WHERE year = '2016'
			GROUP BY region
)

SELECT 
	es.region
	, ROUND (cesg.clean_energy_sum / es.energy_sum * 100, 2) AS clean_energy_generation_ratio_2016
FROM clean_energy_sum_gen cesg
LEFT JOIN energy_sum_2016 es on es.region = cesg.region

```
Next, I will calculate the general clean energy generation and capacity ratios for 2016. This data will allow me to compare the potential of clean energy generation with its actual capacity, offering insights into the progress and efficiency of Canada’s green transition.

This calculation will also utilize the Common Table Expression (CTE) function to streamline the analysis.

```
WITH clean_generation_ratio_2016 AS
(
SELECT 
	ROUND (SUM (data) /
			(
				SELECT 
				SUM (data)
				FROM electricity_generation_table
				WHERE year = '2016'
) * 100, 2) AS clean_generation_ratio_2016
 , ROW_NUMBER() OVER (Partition by SUM(data)) as mbr
FROM electricity_generation_table
WHERE year = '2016' and source != 'Coal' and source != 'Natural Gas' and source != 'Oil and Diesel' 
)
, clean_capacity_ratio_2016 AS
(
SELECT 
	ROUND (SUM (data) /
			(
				SELECT 
				SUM (data)
				FROM electricity_capacity_table
				WHERE year = '2016'
) * 100, 2) AS clean_capacity_ratio_2016
	, ROW_NUMBER() OVER (Partition by SUM(data)) AS mbr
FROM electricity_capacity_table
WHERE year = '2016' and source != 'Coal' and source != 'Natural Gas' and source != 'Oil and Diesel' 
)

SELECT 
	cgr.clean_generation_ratio_2016
	, ccr.clean_capacity_ratio_2016
	, cgr.mbr
FROM clean_generation_ratio_2016 cgr
LEFT JOIN clean_capacity_ratio_2016 ccr on ccr.mbr = cgr.mbr
```
Using all this information, I created a dashboard to visualize the state of Canada's green energy transition.

### 2. Data visualization in Power BI

![image](https://github.com/user-attachments/assets/bce71bc9-0b16-4c08-9cfa-becf6df9a881)

![image](https://github.com/user-attachments/assets/9d4499e6-151e-4649-b78e-647b9651b0fb)

### 3. Final conclusions

The dashboard includes four key indicators:

- Total Capacity: Displays the overall power capacity of Canada’s energy system.

- Clean Energy Capacity Ratio: Shows the proportion of clean energy within the total capacity.

- Total Generation: Represents the total amount of electricity generated.

- Clean Energy Generation Ratio: Indicates the share of clean energy in the total electricity generation.

These indicators enable users to track the progress of Canada’s green energy transition in terms of both capacity and generation. They also provide insights into the development trajectory of the energy system.

Additionally, the dashboard features two pie charts illustrating the source structure of power capacity and power generation for 2016. While the structures appear similar at first glance, there are notable differences that are crucial for understanding the energy system. For example:

- Natural gas ranks as the second-largest energy source in the capacity structure but falls to fourth place in the generation structure.
- Similar disparities exist for other energy sources.

The dashboard also allows users to track changes in power generation and capacity for specific energy sources over time, from 2005 to 2016.

For instance, the image below illustrates the development of coal power capacity and generation since 2005. The dashboard also allows users to track the total power capacity and generation for specific energy sources over time, providing valuable insights into their trends and evolution.

![image](https://github.com/user-attachments/assets/4f7837a9-40a4-42d5-8d6d-5be0f94bb123)

For comparison, this image highlights the development of another energy source - wind power.

![image](https://github.com/user-attachments/assets/ae70cd6d-c8ed-47d4-910d-7ab318c36026)

The second page of the dashboard highlights the distinction in power generation and capacity across different regions of Canada. Users can view a map of Canada, illustrating the variation in power generation among regions for the year 2016. The stacked bar chart on the right shows the clean energy generation ratio for each region. Below, area charts depict the development of power generation and capacity for each specific region.

![image](https://github.com/user-attachments/assets/3fec5843-3e19-4df8-a039-55c55633c11f)

## Perspectives

This dataset offers potential for further development, particularly through integration with other data sources. Additionally, various other indicators can be calculated using only this dataset. For example, the clean energy generation ratio could be calculated by region and year.







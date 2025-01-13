# Analysis of Canada`s Electricity System using PostgreSQL and Power BI 
Analisys of Canada`s Electricity System using free Data from Open Government portal of Canada via https://open.canada.ca/data/en/dataset/2cdf43fc-d4aa-4604-9f21-29777d955810. Dataset 'Electricity generation and capacity in Canada 2005-2016'

This repository describes process and results of analysis of the Canadian electricy system, particulary generation and capacity of the system. 

This project is my first try of analyzing open data using PostgreSQL and Power BI. 

TERMS AND DEFINITIONS

Power capacity is the amount of electricity a generator can produce when it's running at full blast.
Measure unit is Megawatt (MW)

Power generation is the process of generating electric power from sources of primary energy.
Measure unit is Gigawatt hours (GW/h)

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

1. DATA ANALYSIS IN POSTGRESQL

At first I want to know the amount of power capacity and power generation for the last year (2016).

SELECT *
FROM electricity_capacity_table
WHERE year = '2016'



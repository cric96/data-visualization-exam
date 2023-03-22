# Data Visualization for Scientists 
## Climate change analysis

**Report done by Gianluca Aguzzi - 22/03/23**

The objective of this project is to showcase the impact of climate change on global temperatures over the last few years. 
The dataset used for this analysis was sourced from a Kaggle competition 
([https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data](https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data))
and includes the average temperatures recorded in various countries and cities. 
To visualize this data, I implemented an interactive application 
that allows to see the overall trend of global temperatures,
and to compare the temperature changes in different countries and cities.
The web page was implemented using chart.js and Vue.js.
The resulting visualization can be accessed through the following link: ([https://cric96.github.io/climate-change-analysis/](https://cric96.github.io/climate-change-analysis/))

## Data Analysis and structure
As a first step, I conducted a thorough analysis of the dataset to gain an understanding of its structure and to identify the most interesting features for visualization purposes. The results of this analysis can be found in the data_analysis.ipynb notebook, which is accessible at the following GitHub repository: https://github.com/cric96/data-visualization-exam.

The dataset chosen for this project comprises multiple files, 
including `GlobalTemperatures.csv`, `GlobalLandTemperaturesByState.csv`, `GlobalLandTemperaturesByCountry.csv`, 
`GlobalLandTemperaturesByCity.csv`, and `GlobalLandTemperaturesByMajorCity.csv`. 
For this project, I focused solely on the first dataset, which provides the global average monthly temperature readings.

The columns in this dataset include:

- `dt`: The date when the temperature data was recorded (samples taken monthly)
- `LandAverageTemperature`: The global average land temperature in Celsius
- `LandAverageTemperatureUncertainty`: The 95% confidence interval around the average
- `LandMaxTemperature`: The global average maximum land temperature in Celsius
- `LandMaxTemperatureUncertainty`: The 95% confidence interval around the average
- `LandMinTemperature`: The global average minimum land temperature in Celsius
- `LandMinTemperatureUncertainty`: The 95% confidence interval around the average
- `LandAndOceanAverageTemperature`: The global average land and ocean temperature in Celsius
- `LandAndOceanAverageTemperatureUncertainty`: The 95% confidence interval around the average

Although the dataset was already clean, 
I performed some preprocessing to make it more suitable for visualization purposes. 
Firstly, I removed temperature readings before 1900, as they were not relevant for the analysis and had high levels of uncertainty. 
Additionally, to reduce the overall dataset size, I only included the average temperature readings for each year computed from the monthly readings.
Lastly, I extracted the top 20 worst delta changes in temperature (i.e., the temperature difference from the 1900 average temperature) 
for each year to determine which countries are the most affected by the climate change.

To reduce the overall dataset size, I produce several json that precompute the data that will be used for the visualization.
The resulting json files are stored in the `data` folder and are the following:
- `data-viz/src/assets//average_temperatures.json`: Contains the average temperature for each year. The json structure is the following: `{ <year>: { <country>: { "average_temperature": X, "average_temperature_uncertainty": Y } } }` where year and country are the key of there json object (so they could be 1900 and Italy for instance)  and X and Y are correspondingly the average temperature and the uncertainty for that year and country.
- `data-viz/src/assets/global_delta.json`: Contains the overall delta change in temperature for each year, the json is structured as follows: `{<year>: {"delta": X}}`
- `data-viz/src/assets/rank.json`: Contains the top 20 worst delta changes in temperature for each year. The json structure is: `{<year>: [ "country", delta ]}` where country is the country with the worst delta change in temperature and delta is the delta change in temperature for that country. The list is ordered by the delta change in temperature for that year.
- `data-viz/src/assets/min_max_for_each_country.json`: Contains the average temperature for each year for each country. The structure of the json object is: `{<country>: { min: X, max: Y }`

The structure of the file was made to be easily accessible from the web page,
and to optimize the loading time of the page.
Indeed, the json files are loaded only once, and then the data is stored in the Vue.js store.
Furthermore, since most of the time the query are made by year and country,
using them as index of the json object allows to access the data in constant time.
## Web Page and chart design
The web page should show the average temperature increasing over the years, and the countries that are the most affected by the climate change.
As a first step, I tried to create a simple mock for the resulting web page, to have a clear idea of what I wanted to achieve.
Particularly, the mock for this site was the following:

![Image](https://user-images.githubusercontent.com/23448811/226698615-5c0c61ef-0361-4583-9dd5-87291abf61d8.png)

I thought to divide the page in three main sections:
- The first section should show the overall trend of global temperatures, and the countries that are the most affected by the climate change.
- The second part shows the top 20 worst delta changes in the average temperature in the selected year
- The third part shows the average temperature increasing over the years in the selected country

The website is designed to be interactive, allowing users to select a specific year and country to view corresponding climate data. Users can also start a simulation to observe how temperature has changed over time.

The data collected is spatio-temporal in nature, as it includes both the temporal aspect (year) and spatial aspect (country). The spatial aspect is represented visually through a map, with each country's color determined by its average temperature. To better convey the severity of temperature changes and its relation to climate change risk, an orange-red colormap is used.

Although the map is static initially, it becomes dynamic when users select a year and start the animation. Additionally, a line chart is used to display the average temperature increasing over time for the selected country to showcase the temporal aspect of the dataset, while a bar chart highlights the top 20 countries experiencing the worst delta changes in average temperature for the selected year. 
This provides an overview of how climate change is impacting countries worldwide.

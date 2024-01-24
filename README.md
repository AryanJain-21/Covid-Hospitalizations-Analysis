

Aryan Jain - [aryan.km.jain@gmail.com](mailto:aryan.km.jain@gmail.com)


### Introduction

“COVID-19 is a respiratory disease caused by SARS-CoV-2, a new coronavirus discovered in 2019. Many of the pre-existing conditions that increase the risk of hospitalization in those with COVID-19 are the same diseases that are affected by long-term exposure to air pollution.

Please investigate whether long-term average exposure to low air quality environments is associated with an increased risk of COVID-19 hospitalization in the United States.”


### Did the lockdown result in a noticeable improvement in air quality? Most notably in what regions?

As studies have shown, the air quality improved quite a bit worldwide during the lockdown.[^1] However, the question posed is whether or not the improvement in air quality in the United States is noticeable.

There were two datasets imported from the US EPA, a dataset containing air quality data from 1980 and another containing air quality data from 2021 for the United States. To clearly visualize and see the difference between the air quality index (AQI) in 1980 and 2021, I took the average AQI over the year per state. The dataset is set up to give the AQI for counties in the states. Instead I merged the county data and took the average of all the AQIs for each state they belonged in. I did this to visualize the effects the lockdown had on AQI more clearly. 

To visualize the average AQIs, I used Plotly’s heatmap. To put all my data accurately on the map of the United States, I pulled in a dataset containing the longitudes and latitudes for the states and added the state location to the dataset containing the AQIs ([Kaggle Data Source](https://www.kaggle.com/datasets/tennerimaheshwar/us-state-and-territory-latitude-and-longitude-data?resource=download)). After creating the heatmap I decided to remove the density heatmap and instead just assign dots representing the average AQI on every data point (State/Territory) within the dataset. This method proved to be clearer and represented the results well.

The 1980 AQI Heatmap, as shown below, displays that in many parts of the county, the average AQI was significantly high, particularly in the Northeast. 

![image](https://github.com/AryanJain-21/EMD-Challenge/assets/149397817/13115afa-8944-4ea1-9eb8-cb91c4622e7a)


The 2021 AQI heatmap, as shown below, on the contrary has a very noticeable improvement in average AQI throughout the country. **It is clear that the lockdown in 2021 had a very noticeable improvement in air quality. Especially in regions like the Northeast and some of the South.** This correlates with a study showing that the top three biggest decreases in air pollution during lockdown in America happened in Washington DC with -21.1%, New York with -20.7%, and Boston with -18.5%.[^2] 

![image](https://github.com/AryanJain-21/EMD-Challenge/assets/149397817/ae472e13-8434-4a91-992e-b1f0b3577119)


### Is there a correlation between Air Quality Index (AQI) and COVID-19 hospitalization rate?

It is clear, with many studies and reports backing the same conclusion, that long-term exposure to bad air pollution (a bad Air Quality Index) is positively associated with increased COVID-19 cases[^3], and that could imply that bad air pollution is also correlated with COVID-19 hospitalizations. To test this theory, I worked with the Air Data files from the US EPA and the COVID-19 case file from the CDC. To make the hospitalization into a rate (hospitalization / State population), I used the estimated 2021 population data from the US Census ([Population Data](https://www.census.gov/data/tables/time-series/demo/popest/2020s-state-total.html)). After manipulating and engineering the data _(more in-depth in later sections)_, I used the ‘.corr’ method, which is part of the Pandas library. The correlation coefficient between the average AQI and hospitalization rate per state that was displayed was 0.291. **The magnitude of this correlation coefficient suggests a moderate positive correlation between the air quality index and hospitalization rate, meaning that as the AQI increases (air quality gets worse), the hospitalization rate will also increase.**


### Are there any confounding factors you should control for?

When checking the correlation between the Air Quality Index (AQI) and the COVID-19 hospitalization rate, **I identified outside exposure, underlying diseases, and old age as confounding factors**. The exposure column states if the COVID-19 patient, in the 14 days prior to illness onset, has any of the following known exposures: domestic travel, international travel, etc. The case being recorded in a different location than where the patient contracted the disease creates a problem when trying to figure out the effect of a location’s AQI and that same location’s hospitalization rate. If a person contracted COVID-19 and either has an underlying disease or is above the age of 65<sub><strong> </strong></sub>, the risk of hospitalization or serious health problems increases significantly regardless of the AQI.[^4] 


### How would you present the results visually? How do you label your axes?

_How I presented the Air Quality changes in lockdown visually was already covered in the first section._ 

**To present the correlation between the Air Quality Index (AQI) and the COVID-19 hospitalization rate, I used the Seaborn library to create a scatterplot and line of best fit.** I used this type of graph to show the spread and patterns of data, and the line of best fit to show the relation between the axes (the Correlation Coefficient). **I put the Average AQI on the x-axis (The ‘Independent variable’) and the Rate of Hospitalization on the y-axis (The ‘Dependent variable’) to visually show how changing the AQI affects the rate of Hospitalization amongst COVID-19 patients.** _Note: Originally, I converted the rates of hospitalization to percentages but decided to stick with the numerical values because it was easier to work with the data_.

![image](https://github.com/AryanJain-21/EMD-Challenge/assets/149397817/d80abc76-1221-48a5-889e-339da5887614)



### How did you treat missing data?

When checking the contents of the hospitalization data in the COVID cases file, about 18 million rows were defined, but 15 million rows were missing. **The magnitude of the missing rows was too high to ignore during analysis, so imputing it was the best option**.

My first method I got a standard rate of how many cases ended up resulting in hospitalization from the 18 million rows that were defined. Then I used the sample method from the pandas library to randomly assign the 15 million missing rows ‘Yes’. This method was simple, but it resulted in much variability. As rows were assigned randomly, they didn’t have to correlate with any possible trends and could skew some data. To analyze the data, I wanted to remove as much variability as possible.

**The second method I was required to use was the sklearn library and the linear regression Machine Learning algorithm.** Since I wanted to remove the variability of the standard rate method, I chose to train and test a model with my known dataset and predict the possible outcomes over the missing dataset. Numerous studies have shown that age and gender actually have been useful in predicting COVID-19 hospitalizations/severity<sub> </sub>[^5], so I decided to include those categories, as well as categories listing if the disease was lethal, and the weeks between the earliest date and the first positive of COVID-19 collected as well as symptoms onset. These categories helped me train a model that predicted whether a row of data could be assigned ‘Yes’ for hospitalization or not. Since I was given a percent, I tested several thresholds for what should be considered a ’Yes’ until finalizing that 55% was good. I chose 55% after looking at how many cases resulted in false classifications of ‘Yes’ and ‘No,’ and because at 55%, the cases of false yes’ and false no’s were about the same size, therefore canceling each other out. Since this model is not being used to predict future cases and has no real-world repercussions, I settled that this model worked well. I applied this model to the 15 million rows of missing data and was able to assign all values that were predicted equal to or above 55% as ‘Yes’ and below 55% as ‘No.’ _Note: within the Hospitalization column, I treated the ‘Unknown’ category as if it is the same as the ‘No’ category. Since this project focuses on the ‘Yes’ category, I didn’t complicate the analysis and moved forward._


### Conclusions, Future work

After working with the data for about two days I am confident in my findings, but realize that it is hard to properly predict the correlation coefficient of hospitalization rate and average AQI. In the future, to improve upon this model, honing in more with the average AQI could result in even clearer results. Every state has many parts that are not as populated as others, and therefore, getting the average AQI by state and not county hinders some of the results. Furthermore, with missing data amongst confounding variables, it is truly impossible to fully remove the effects they have on the data, yet with that considered, removing as much as was done was proficient, and trying to predict missing values within the confounding variables would again unnecessarily complicate the project for the timeframe that was given. I also considered the correlation between hospitalization rate and average AQI but narrowed down the focus to those who had been officially diagnosed with COVID-19. My thought process was that since AQI and contracting COVID-19 had already proven to correlate if we assume COVID-19, we reduce the number of people we are looking at and might find more interesting results in terms of correlation. Though I realized that method did not garner many results and thus decided to leave it out of my writeup, it still exists in the code. For the heatmaps, not all states/territories were able to be displayed as there were either gaps in the AQI data or the latitude/longitude data, but once again, there did not seem to be a need to go through the extra effort to fill in this missing data, as the general conclusion can still be drawn without it. Overall, this project was a lot of fun; working with complex datasets and having to figure out ways to solve the problems presented to me while backing up my methods and results with real figures and sources was a fun and distinct challenge not seen very often in an academic setting. 


<!-- Footnotes themselves at the bottom. -->
## Notes

[^1]:
     Saha, Lala et al. “[The impact of the COVID-19 lockdown on global air quality: A review](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8819204/).” _Environmental sustainability (Singapore)_ vol. 5,1 (2022): 5-23. doi:10.1007/s42398-021-00213-6. (Accessed: 22 January 2024)

[^2]:
     Strong, S. (2023) _[Air pollution dropped during pandemic lockdowns, University of Houston](https://uh.edu/news-events/stories/2022-news-articles/may-2022/05092242022-uh-covid-air-pollutant-pollution-ghahremanloo-choi.php#:~:text=The%20biggest%20air%20quality%20improvement,Washington%20DC%2C%20%2D21.1%25)_. (Accessed: 23 January 2024). 

[^3]:
     Hernandez Carballo, Ireri et al. “[The impact of air pollution on COVID-19 incidence, severity, and mortality: A systematic review of studies in Europe and North America.](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9420033/#:~:text=Long%2Dterm%20exposure%20to%20air%20pollution%20is%20more%20frequently%20positively,compared%20to%20short%2Dterm%20exposure.)” _Environmental research_ vol. 215,Pt 1 (2022): 114155. doi:10.1016/j.envres.2022.114155. (Accessed: 23 January 2024).

[^4]:
    <sup> <em><a href="https://www.mayoclinic.org/diseases-conditions/coronavirus/in-depth/coronavirus-who-is-at-risk/art-20483301#:~:text=The%20risk%20of%20developing%20dangerous,systems%2C%20obesity%2C%20or%20diabetes.">Covid-19: Who’s at higher risk of serious symptoms?</a></em></sup>, _Mayo Clinic_. (Accessed: 23 January 2024).

[^5]:
    <sup> </sup>Cisterna-García, Alejandro et al. “[A predictive model for hospitalization and survival to COVID-19 in a retrospective population-based study](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9614188/#:~:text=We%20identified%20age%2C%20gender%2C%20and,%2C%20and%20hypertension22%2C23.).” Scientific reports vol. 12,1 18126. 28 Oct. 2022, doi:10.1038/s41598-022-22547-9. (Accessed: 23 January 2024)

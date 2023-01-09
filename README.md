# ***Data Analysis of Covid - 19 Worldwide using SQL***

## _1. Total Covid-19 Cases vs Infected Cases._

### _Continent - Total Covid-19 Cases vs Infected Cases Ordered By Continent._
``` sql server
SELECT continent, 
SUM(total_cases) AS total_cases, 
SUM(new_cases) AS infected_cases,
SUM(CAST(total_deaths AS int)) AS total_deaths, 
MAX(population) AS populations
FROM CovidDeaths 
WHERE continent IS NOT null
GROUP BY continent
ORDER BY continent
```
### _Countries - Total Covid-19 Cases vs Infected Cases Ordered By location._
``` sql server
SELECT distinct(location), 
MAX(total_cases) AS total_cases, 
MAX(new_cases) AS infected_cASes,
MAX(total_deaths) AS total_deaths, 
MAX(population) AS populations
FROM CovidDeaths 
WHERE continent IS NOT null
GROUP BY location
ORDER BY location
```
##
## _2. Total Cases vs Total Deaths._

### _Continent - Total Cases vs Total Deaths._
``` sql server
SELECT distinct(continent), 
SUM(total_cases) AS total_cases,
SUM(CAST(total_deaths AS FLOAT)) AS total_deaths,
((SUM(CAST(total_deaths AS FLOAT)))/(SUM(total_cases))*100) AS death_percentage
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY continent
ORDER BY continent
```
### _Countries - Total Cases vs Total Deaths._
``` sql server
SELECT distinct(location), 
MAX(total_cases) AS total_cases, 
MAX(total_deaths) AS total_deaths , 
(MAX(total_deaths)/MAX(total_cases)*100) AS death_percentage
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY location
ORDER BY location
```
##
## 3. _Survival rate if contracted Covid-19._

### _Continent - Survival rate if contracted Covid-19._
``` sql server
SELECT continent, SUM(total_cases) AS total_cases, 
SUM(CAST(total_deaths AS FLOAT)) AS total_deaths, 
((SUM(CAST(total_deaths AS FLOAT)))/(SUM(total_cases))*100) AS death_percentage,
(100 - ((SUM(CAST(total_deaths AS FLOAT)))/(SUM(total_cases))*100)) AS survival_rate_percentage
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY continent
ORDER BY continent
```
### _Countries - Survival rate if contracted Covid-19._
``` sql server
SELECT location, 
MAX(total_cases) AS total_cases, 
MAX(total_deaths) AS total_deaths, 
(MAX(total_deaths)/MAX(total_cases))*100 AS death_percentage,
(100 - (MAX(total_deaths)/MAX(total_cases))*100) AS survival_rate_percentage
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY location
ORDER BY location
```
##
### _UAE - Survival rate if contracted Covid-19._
``` sql server
SELECT location, 
MAX(total_cases) AS total_cases, 
MAX(total_deaths) AS total_deaths, 
(MAX(total_deaths)/MAX(total_cases))*100 AS death_percentage,
(100 - (MAX(total_deaths)/MAX(total_cases))*100) AS survival_rate_percentage
FROM CovidDeaths
WHERE location='United Arab Emirates'
GROUP BY location
ORDER BY location
```
##
## _4. Total Cases vs Populations_

### _Continent - Total Cases vs Populations_
``` sql server
SELECT distinct(continent), 
SUM(total_cases) AS total_cases, 
SUM(population) AS population,
((SUM(total_cases))/(SUM(population))*100) AS covid_percentage
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY continent
ORDER BY continent
```
### _Countries - Total Cases vs Populations_
``` sql server
SELECT distinct(location), 
MAX(total_cases) AS total_cases, 
MAX(population) AS population,
((MAX(total_cases))/(MAX(population))*100) AS covid_percentage
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY location
ORDER BY location
```
##
## _5.  Highest infection rate in comparison to Populations._

### _Countries - Highest infection rate in comparison to Populations_
``` sql server
SELECT location, 
MAX(total_cases) AS infectiON_count, 
population, MAX((total_cases/population)*100) AS infected_percentage
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY location, population
ORDER BY infected_percentage desc
```
##
## 6. _Highest Death count in comparison to Populations._

### _Continents - With Highest Death count in comparison to Populations_
``` sql server
SELECT continent,
SUM(CAST(total_deaths AS bigint)) AS total_death_count
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY continent
ORDER BY total_death_count desc
```
### _Countries - With Highest Death count in comparison to Populations_
``` sql server
SELECT location, 
MAX(CAST(total_deaths AS bigint)) AS total_death_count
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY location
ORDER BY total_death_count desc
```
##
## _7. Global Comparison - New Covid-19 cases vs total death count with death percentage ordered by date_

### _Grouped by Date_
``` sql server
SELECT date, SUM(new_cases) AS total_new_count, 
SUM(CAST(new_deaths AS int)) AS total_death_count,
SUM(CAST(new_deaths AS int))/SUM(new_cases)*100 AS death_percentage
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY date
ORDER BY date
```
##
## _8. Global Comparison - Total New Cases Count, Death Count, Death Percentage_
``` sql server
SELECT SUM(new_cases) AS total_new_count, 
SUM(CAST(new_deaths AS int)) AS total_death_count,
SUM(CAST(new_deaths AS int))/SUM(new_cases)*100 AS death_percentage
FROM CovidDeaths
WHERE continent IS NOT null
```
##
## _9. Global Comparison - Total Populations vs Total Vacinations_

### _Continent -  Total Populations vs Total Vacinations_
``` sql server
SELECT CD.continent, 
SUM(CD.population) AS populations, 
SUM(CAST(CV.new_vaccinatiONs AS bigint)) AS new_vaccinatiONs
FROM CovidDeaths CD
JOIN CovidVacinatiONs CV
ON CD.location=CV.location
and CD.date=CV.date
WHERE CD.continent IS NOT null
GROUP BY CD.continent
ORDER BY CD.continent
```
### _Countries -  Total Populations vs Total Vacinations_
``` sql server
SELECT CD.location, 
MAX(CD.population) AS population, 
SUM(CAST(CV.new_vaccinatiONs AS bigint)) AS new_vaccinatiONs
FROM CovidDeaths CD
JOIN CovidVacinatiONs CV
ON CD.location=CV.location
and CD.date=CV.date
WHERE CD.continent IS NOT null
GROUP BY CD.location
ORDER BY CD.location
```
##
## _10. Continents location sum of New Vacinations per Date._
``` sql server
SELECT CD.continent, 
CD.location, 
CD.date, 
CD.population, 
CV.new_vaccinatiONs,
SUM(CAST(CV.new_vaccinatiONs AS bigint)) OVER (partitiON by CD.location ORDER BY CD.location, CD.date) AS People_Vacinated
FROM CovidDeaths CD
JOIN CovidVacinatiONs CV
ON CD.location=CV.location
and CD.date=CV.date
WHERE CD.continent IS NOT null and CV.new_vaccinatiONs IS NOT null
ORDER BY CD.continent,CD.location
```
##
### _Continent's location sum of New Vacinations per Date and Percentage_
### _Using CTE_
``` sql server
With CTE (continent, location, date, population, new_vaccinatiONs,People_Vacinated,People_Vacinated_Percentage)
AS 
(
SELECT CD.continent, CD.location, CD.date, CD.population, CV.new_vaccinatiONs,
SUM(CAST(CV.new_vaccinatiONs AS bigint)) OVER (partitiON by CD.location ORDER BY CD.location) AS People_Vacinated,
(people_vaccinated/population)*100 AS People_Vacinated_Percentage
FROM CovidDeaths CD
JOIN CovidVacinatiONs CV
ON CD.location=CV.location
and CD.date=CV.date
WHERE CD.continent IS NOT null and CV.new_vaccinatiONs IS NOT null
)
SELECT distinct(location),MAX(People_Vacinated_Percentage) AS Percentage_Vacinated FROM CTE
GROUP BY location
ORDER BY Percentage_Vacinated desc
```
##
## _11. Countries that started Vacinations earliest._
``` sql server
SELECT distinct(location), min(date) AS earliested_date
FROM CovidVacinatiONs
WHERE new_vaccinatiONs not in (0,'NULL') and continent IS NOT null
GROUP BY location
ORDER BY earliested_date
```
##
## _12. Countries that started Vacinations late._
``` sql server
SELECT distinct(location), min(date) AS earliested_date
FROM CovidVacinatiONs
WHERE new_vaccinatiONs not in (0,'NULL') and continent IS NOT null
GROUP BY location
ORDER BY earliested_date desc
```

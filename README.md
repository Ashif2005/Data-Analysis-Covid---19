# Data-Analysis-Covid---19
## Data Analysis of Covid - 19 Worldwide using SQL

### 1. Total Covid-19 Cases vs Infected Cases.

### Continent - Total Covid-19 Cases vs Infected Cases Ordered By Continent.
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
### Countries - Total Covid-19 Cases vs Infected Cases Ordered By location.
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
### 2. Total Cases vs Total Deaths.

### Continent - Total Cases vs Total Deaths.
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
### Countries - Total Cases vs Total Deaths.
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
### 3. Survival rate if contracted Covid-19.

### Continent - Survival rate if contracted Covid-19.
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
### Countries - Survival rate if contracted Covid-19.
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
### UAE - Survival rate if contracted Covid-19.
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
### 4. Total Cases vs Populations

### Continent - Total Cases vs Populations
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
### Countries - Total Cases vs Populations
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
### 5.  Highest infection rate in comparison to Populations.

### Countries - Highest infection rate in comparison to Populations
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
### 6. Highest Death count in comparison to Populations.

### Continents - With Highest Death count in comparison to Populations
``` sql server
SELECT continent,
SUM(CAST(total_deaths AS bigint)) AS total_death_count
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY continent
ORDER BY total_death_count desc
```
### Countries - With Highest Death count in comparison to Populations
``` sql server
SELECT location, 
MAX(CAST(total_deaths AS bigint)) AS total_death_count
FROM CovidDeaths
WHERE continent IS NOT null
GROUP BY location
ORDER BY total_death_count desc
```
##
### 7. Global Comparison - New Covid-19 cases vs total death count with death percentage ordered by date

### Grouped by Date
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
### 8. Global Comparison - Total New Cases Count, Death Count, Death Percentage
``` sql server
SELECT SUM(new_cases) AS total_new_count, 
SUM(CAST(new_deaths AS int)) AS total_death_count,
SUM(CAST(new_deaths AS int))/SUM(new_cases)*100 AS death_percentage
FROM CovidDeaths
WHERE continent IS NOT null
```
##
### 9. Global Comparison - Total Populations vs Total Vacinations

### Continent -  Total Populations vs Total Vacinations
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
### Countries -  Total Populations vs Total Vacinations
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
### 10. Continents location sum of New Vacinations per Date.
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
### Continent's location sum of New Vacinations per Date and Percentage
### Using CTE
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
### 11. Countries that started Vacinations earliest.
``` sql server
SELECT distinct(location), min(date) AS earliested_date
FROM CovidVacinatiONs
WHERE new_vaccinatiONs not in (0,'NULL') and continent IS NOT null
GROUP BY location
ORDER BY earliested_date
```
##
### 12. Countries that started Vacinations late.
``` sql server
SELECT distinct(location), min(date) AS earliested_date
FROM CovidVacinatiONs
WHERE new_vaccinatiONs not in (0,'NULL') and continent IS NOT null
GROUP BY location
ORDER BY earliested_date desc
```

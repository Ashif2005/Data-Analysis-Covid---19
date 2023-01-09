# Data-Analysis-Covid---19
Data Analysis of Covid - 19 Worldwide using SQL

1. Total Covid-19 Cases vs Infected Cases.

-- Continent - Total Covid-19 Cases vs Infected Cases Ordered By Continent.

select continent, 
sum(total_cases) as total_cases, 
sum(new_cases) as infected_cases,
sum(cast(total_deaths as int)) as total_deaths, 
max(population) as populations
from CovidDeaths 
where continent is not null
group by continent
order by continent

-- Countries - Total Covid-19 Cases vs Infected Cases Ordered By Location.

select distinct(location), 
max(total_cases) as total_cases, 
max(new_cases) as infected_cases,
max(total_deaths) as total_deaths, 
max(population) as populations
from CovidDeaths 
where continent is not null
group by location
order by location

2. Total Cases vs Total Deaths.

-- Continent - Total Cases vs Total Deaths.

select distinct(continent), 
sum(total_cases) as total_cases,
sum(cast(total_deaths as float)) as total_deaths,
((sum(cast(total_deaths as float)))/(sum(total_cases))*100) as death_percentage
from CovidDeaths
where continent is not null
group by continent
order by continent

-- Countries - Total Cases vs Total Deaths.

select distinct(location), 
max(total_cases) as total_cases, 
max(total_deaths) as total_deaths , 
(max(total_deaths)/max(total_cases)*100) as death_percentage
from CovidDeaths
where continent is not null
group by location
order by location

3. Survival rate if contracted Covid-19.

-- Continent - Survival rate if contracted Covid-19.

select continent, sum(total_cases) as total_cases, 
sum(cast(total_deaths as float)) as total_deaths, 
((sum(cast(total_deaths as float)))/(sum(total_cases))*100) as death_percentage,
(100 - ((sum(cast(total_deaths as float)))/(sum(total_cases))*100)) as survival_rate_percentage
from CovidDeaths
where continent is not null
group by continent
order by continent

-- Countries - Survival rate if contracted Covid-19.

select location, 
max(total_cases) as total_cases, 
max(total_deaths) as total_deaths, 
(max(total_deaths)/max(total_cases))*100 as death_percentage,
(100 - (max(total_deaths)/max(total_cases))*100) as survival_rate_percentage
from CovidDeaths
where continent is not null
group by location
order by location

-- UAE - Survival rate if contracted Covid-19.

select location, 
max(total_cases) as total_cases, 
max(total_deaths) as total_deaths, 
(max(total_deaths)/max(total_cases))*100 as death_percentage,
(100 - (max(total_deaths)/max(total_cases))*100) as survival_rate_percentage
from CovidDeaths
where location='United Arab Emirates'
group by location
order by location

4. Total Cases vs Populations

-- Continent - Total Cases vs Populations

select distinct(continent), 
sum(total_cases) as total_cases, 
sum(population) as population,
((sum(total_cases))/(sum(population))*100) as covid_percentage
from CovidDeaths
where continent is not null
group by continent
order by continent

-- Countries - Total Cases vs Populations

select distinct(location), 
max(total_cases) as total_cases, 
max(population) as population,
((max(total_cases))/(max(population))*100) as covid_percentage
from CovidDeaths
where continent is not null
group by location
order by location

5.  Highest infection rate in comparison to Population.

-- Countries - Highest infection rate in comparison to Population

select location, 
max(total_cases) as infection_count, 
population, max((total_cases/population)*100) as infected_percentage
from CovidDeaths
where continent is not null
group by location, population
order by infected_percentage desc

6. Highest Death count in comparison to Population.

-- Continents - With Highest Death count in comparison to Population

select continent,
sum(cast(total_deaths as bigint)) as total_death_count
from CovidDeaths
where continent is not null
group by continent
order by total_death_count desc

-- Countries - With Highest Death count in comparison to Population

select location, 
max(cast(total_deaths as bigint)) as total_death_count
from CovidDeaths
where continent is not null
group by location
order by total_death_count desc

7. Global Comparison - New Covid-19 cases vs total death count with death percentage ordered by date

-- Grouped by Date

select date, sum(new_cases) as total_new_count, 
sum(cast(new_deaths as int)) as total_death_count,
sum(cast(new_deaths as int))/sum(new_cases)*100 as death_percentage
from CovidDeaths
where continent is not null
group by date
order by date

8. Global Comparison - Total New Cases Count, Death Count, Death Percentage

select sum(new_cases) as total_new_count, 
sum(cast(new_deaths as int)) as total_death_count,
sum(cast(new_deaths as int))/sum(new_cases)*100 as death_percentage
from CovidDeaths
where continent is not null

9. Global Comparison - Total Population vs Total Vacinations

-- Continent -  Total Population vs Total Vacinations

select CD.continent, 
sum(CD.population) as populations, 
sum(cast(CV.new_vaccinations as bigint)) as new_vaccinations
from CovidDeaths CD
join CovidVacinations CV
on CD.location=CV.location
and CD.date=CV.date
where CD.continent is not null
group by CD.continent
order by CD.continent

-- Countries -  Total Population vs Total Vacinations

select CD.location, 
max(CD.population) as population, 
sum(cast(CV.new_vaccinations as bigint)) as new_vaccinations
from CovidDeaths CD
join CovidVacinations CV
on CD.location=CV.location
and CD.date=CV.date
where CD.continent is not null
group by CD.location
order by CD.location

10. Continents Location Sum of New Vacination per Date.

select CD.continent, 
CD.location, 
CD.date, 
CD.population, 
CV.new_vaccinations,
sum(cast(CV.new_vaccinations as bigint)) OVER (partition by CD.location order by CD.location, CD.date) as People_Vacinated
from CovidDeaths CD
join CovidVacinations CV
on CD.location=CV.location
and CD.date=CV.date
where CD.continent is not null and CV.new_vaccinations is not null
order by CD.continent,CD.location

-- Continent's Location Sum of New Vacination per Date and Percentage
--Using CTE

With CTE (continent, location, date, population, new_vaccinations,People_Vacinated,People_Vacinated_Percentage)
as 
(
select CD.continent, CD.location, CD.date, CD.population, CV.new_vaccinations,
sum(cast(CV.new_vaccinations as bigint)) OVER (partition by CD.location order by CD.location) as People_Vacinated,
(people_vaccinated/population)*100 as People_Vacinated_Percentage
from CovidDeaths CD
join CovidVacinations CV
on CD.location=CV.location
and CD.date=CV.date
where CD.continent is not null and CV.new_vaccinations is not null
)
Select distinct(location),max(People_Vacinated_Percentage) as Percentage_Vacinated from CTE
group by location
order by Percentage_Vacinated desc

11. Countries that started vacination earliest.

select distinct(location), min(date) as earliested_date
from CovidVacinations
where new_vaccinations not in (0,'NULL') and continent is not null
group by location
order by earliested_date

12. Countries that started vacination late.

select distinct(location), min(date) as earliested_date
from CovidVacinations
where new_vaccinations not in (0,'NULL') and continent is not null
group by location
order by earliested_date desc

13. 

select location, date, new_case
from CovidDeaths
where

select * from CovidDeaths
where location='iran'
order by date

select distinct(CV.location), 
min(CV.date) as vacination_date, 
min(CD.date) as death_date, 
min(CD.new_cases), 
min(CV.new_vaccinations)
from CovidVacinations CV
join CovidDeaths CD
on CV.location=CD.location
and CV.date=CD.date
where CV.location='iran'
group by CV.location
order by CV.location, CV.date

select distinct(CV.location),
min(CV.date) as 

from CovidVacinations CV
join CovidDeaths CD
on CV.location=CD.location
and CV.date=CD.date
where CD.continent is not NULL
group by CV.location
order by CV.location

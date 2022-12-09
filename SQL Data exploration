select * from covid

CREATE TABLE public.covid_vaccinations
(iso_code VARCHAR(10), continent VARCHAR(20), location VARCHAR(50), date DATE, new_tests BIGINT,
total_tests BIGINT,	total_tests_per_thousand NUMERIC, new_tests_per_thousand NUMERIC, new_tests_smoothed BIGINT,
new_tests_smoothed_per_thousand	NUMERIC, positive_rate NUMERIC,	tests_per_case NUMERIC,
tests_units VARCHAR(20), total_vaccinations BIGINT, people_vaccinated BIGINT, people_fully_vaccinated BIGINT,
new_vaccinations BIGINT, new_vaccinations_smoothed BIGINT, total_vaccinations_per_hundred NUMERIC,
people_vaccinated_per_hundred NUMERIC, people_fully_vaccinated_per_hundred NUMERIC,
new_vaccinations_smoothed_per_million BIGINT, stringency_index NUMERIC,	population_density NUMERIC,
median_age NUMERIC, aged_65_older NUMERIC, aged_70_older NUMERIC, gdp_per_capita NUMERIC, extreme_poverty NUMERIC,
cardiovasc_death_rate NUMERIC, diabetes_prevalence NUMERIC,	female_smokers NUMERIC,	male_smokers NUMERIC,
handwashing_facilities NUMERIC,	hospital_beds_per_thousand NUMERIC,	life_expectancy NUMERIC,
human_development_index NUMERIC)

select * from covid
where continent is not null
order by 3,4

--SELECT DATA THAT WE ARE GOING TO BE USING

SELECT location, date, total_cases, new_cases, total_deaths, population
FROM covid
where continent is not null
order by 1,2 

---lookng at total cases vs total deaths
---shows likelihood of dying if you contract covid in your country
SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as death_percentage
FROM covid
where continent is not null and location = 'Nigeria'
order by 1,2

----looking at the total cases vs the population
----shwos waht percentage of the population got covid
SELECT location, date, population, total_cases, (total_cases/population)*100 as PercentPopulationInfected
FROM covid
where continent is not null
order by 1,2

----looking at countries with highest infection rate compared to population
SELECT location, population, max(total_cases) as highestInfectionCount,
MAX((total_cases/population))*100 as PercentPopulationInfected
FROM covid
where continent is not null
GROUP BY 1,2
order by PercentPopulationInfected desc

---showing countries with highest death count per population
SELECT location, max(total_deaths) as TotalDeathCount
FROM covid
where continent is not null
GROUP BY 1
order by 2 desc

---breaking things down by continent
-----------showing the continents with the highest death count

SELECT continent, max(total_deaths) as TotalDeathCount
FROM covid
where continent is not null
GROUP BY 1
order by 2 desc

--------GLOBAL NUMBERS

SELECT SUM(new_cases) as total_cases, sum(new_deaths) as total_deaths, SUM(new_deaths)/SUM(new_cases)*100 as DeathPercentage
FROM covid
where continent is not null
--GROUP BY 1
order by 1,2

------LOOKING AT TOTAL POPULATION VS VACCINATIONS

SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(vac.new_vaccinations) OVER (Partition by dea.location order by dea.location, dea.date)
from covid dea as RollingPeopleVaccinated
join covid_vaccinations vac
	on dea.location=vac.location
	and dea.date=vac.date
where dea.continent is not null
order by 2,3


--------USE CTE

WITH PopvsVac
as (SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(vac.new_vaccinations) OVER (Partition by dea.location order by dea.location, dea.date)
as RollingPeopleVaccinated
from covid dea
join covid_vaccinations vac
	on dea.location=vac.location
	and dea.date=vac.date
where dea.continent is not null)
----order by 2,3

select *, (RollingPeopleVaccinated/population)*100 from PopvsVac


------------------------------------------TEMP TABLE

--CREATE TABLE PercentPopulationVaccinated 
---()
--Continent varchar (255),
--Location varchar(255),
--Date date,
--Population Numeric,
--New_vaccinatons numeric,
--RollingPeopleVaccinated numeric
--)


CREATE TEMP TABLE PercentPopulationVaccinated AS
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(vac.new_vaccinations) OVER (Partition by dea.location order by dea.location, dea.date)
as RollingPeopleVaccinated
from covid dea
join covid_vaccinations vac
	on dea.location=vac.location
	and dea.date=vac.date
where dea.continent is not null
----order by 2,3

---------ROLLING PEOPLE VACINNATED PER POPULATION
select *, (RollingPeopleVaccinated/population)*100 AS RollingPercentVaccinated
from PercentPopulationVaccinated



-----------------CREATING VIEW TO STORE DATA FOR LATER VIZ
Create view PercentPopulationVaccinated as
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(vac.new_vaccinations) OVER (Partition by dea.location order by dea.location, dea.date)
as RollingPeopleVaccinated
from covid dea
join covid_vaccinations vac
	on dea.location=vac.location
	and dea.date=vac.date
where dea.continent is not null

select * from covid

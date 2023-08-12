SELECT * FROM PortfolioProject.dbo.CovidDeath
ORDER BY 3,4;

--t SELECT * FROM PortfolioProject.dbo.CovidVacinations
--t ORDER BY 3,4;

--t Select the Data that we are going to use
SELECT location, date, total_cases, new_cases, total_deaths, population FROM PortfolioProject.dbo.CovidDeath
ORDER BY 1,2;

--t Changed the colomn type TotalCases & TotalDeaths from nvarchar to Decimal(18,2) to allow aggregation.
ALTER TABLE dbo.coviddeath ALTER COLUMN total_cases decimal(18,2);
ALTER TABLE dbo.coviddeath ALTER COLUMN total_deaths decimal(18,2);

--p Looking at Total Cases vs Total Deaths

--p Shows the likelihood of dying if you contact civid in Egypt.
SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 AS DeathPercentage
FROM PortfolioProject.dbo.CovidDeath
WHERE location LIKE '%egypt%'
ORDER BY 1,2

--p Looking at Total cases VS Population.
--p Shows what percentage of population in Egypt got covid.
SELECT location, date, population, total_cases, (total_cases/population)*100 AS PercentOfPopulationInfected
FROM PortfolioProject.dbo.CovidDeath
WHERE location LIKE '%egypt%'
ORDER BY 1,2

--p Looking at countries with highest infection rate compared to population.
SELECT location, population, MAX(total_cases) AS HighestInfectionCount, MAX((total_cases/population))*100 AS PercentOfPopulationInfected
FROM PortfolioProject.dbo.CovidDeath
GROUP BY location, population
ORDER BY 4 DESC

--p Showing countries with highest death count per population.
SELECT location, MAX(CAST(Total_deaths AS INT)) AS TotalDeathCount
FROM PortfolioProject.dbo.CovidDeath
WHERE continent IS NOT NULL
GROUP BY location
ORDER BY 2 DESC

--p Lets break things by continent
SELECT location, MAX(CAST(Total_deaths AS INT)) AS TotalDeathCount
FROM PortfolioProject.dbo.CovidDeath
WHERE continent IS NULL
GROUP BY location
ORDER BY 2 DESC

-- Global Numbers
SELECT SUM(new_cases) AS SumOfNewCases, SUM(CAST(new_deaths AS INT)) AS SumofNewDeaths, SUM(CAST(new_deaths AS INT))/SUM(new_cases)*100 AS PercentageOfDeathPerCase
FROM PortfolioProject.dbo.CovidDeath
WHERE continent IS NOT NULL AND new_cases <> 0
ORDER BY 2 DESC

-- Looking at Total Population VS Vacinations
SELECT dea.continent, dea.location,dea.date,population, vac.new_vaccinations
, SUM(CONVERT(bigint, vac.new_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date)
 AS RollingPeopleVacinated
-- , (RollingPeopleVacinated/Population)*100
FROM PortfolioProject.dbo.CovidDeath dea
JOIN PortfolioProject.dbo.CovidVacinations vac
ON dea.location = vac.location
AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
ORDER BY 2,3


--p USE CTE

WITH PopVsVac (Continent, location, date, population,New_vaccinations, RollingPeopleVacinated)
AS
(
SELECT dea.continent, dea.location,dea.date,population, vac.new_vaccinations
, SUM(CONVERT(bigint, vac.new_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date)
 AS RollingPeopleVacinated
FROM PortfolioProject.dbo.CovidDeath dea
JOIN PortfolioProject.dbo.CovidVacinations vac
ON dea.location = vac.location
AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
--ORDER BY 2,3
)
SELECT *, (RollingPeopleVacinated/population)*100 FROM PopVsVac

-- Temp Table

DROP TABLE IF EXISTS #PercentPopulationVacinated
CREATE TABLE #PercentPopulationVacinated
(
Continent NVARCHAR (255),
Location NVARCHAR (255),
Date DATETIME,
population NUMERIC,
New_vacinations NUMERIC,
RollingPeopleVacinated NUMERIC
)
INSERT INTO #PercentPopulationVacinated
SELECT dea.continent, dea.location,dea.date,population, vac.new_vaccinations
, SUM(CONVERT(bigint, vac.new_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date)
 AS RollingPeopleVacinated
FROM PortfolioProject.dbo.CovidDeath dea
JOIN PortfolioProject.dbo.CovidVacinations vac
ON dea.location = vac.location
AND dea.date = vac.date
--WHERE dea.continent IS NOT NULL
ORDER BY 2,3
SELECT *, (RollingPeopleVacinated/population)*100
FROM #PercentPopulationVacinated	

--Playing around

SELECT * FROM PortfolioProject..CovidDeath
SELECT * FROM PortfolioProject..CovidVacinations

SELECT location, date, new_cases 
FROM PortfolioProject..CovidDeath
ORDER BY date DESC

--Using Partition

SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(BIGINT, NEW_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS RollingPeapleVaccinated
--, (RollingPeapleVaccinated/population)*100, error, we need to create a CTE or TEMP Table
FROM PortfolioProject..CovidDeath dea
JOIN PortfolioProject..CovidVacinations vac
ON
dea.date=vac.date
AND
dea.location=vac.location
WHERE dea.Continent IS NOT NULL
ORDER BY 2,3


--USE CTE:

WITH PopVsVac (continent, location, date, population, New_vaccinations, RollingPeopleVaccinated)
AS
(
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(BIGINT, NEW_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS RollingPeapleVaccinated
--, (RollingPeapleVaccinated/population)*100, error, we need to create a CTE or TEMP Table
FROM PortfolioProject..CovidDeath dea
JOIN PortfolioProject..CovidVacinations vac
ON
dea.date=vac.date
AND
dea.location=vac.location
WHERE dea.Continent IS NOT NULL
--DER BY 2,3
)
SELECT * , (RollingPeopleVaccinated/population)*100 AS PercantageOfTotalPeopleVacinated
FROM PopVsVac
 
--TEMP TABLE
DROP TABLE IF EXISTS #PercentPopulationVaccinated
CREATE TABLE #PercentPopulationVaccinated
(
continent NVARCHAR (255),
location NVARCHAR (255),
date DATETIME,
population NUMERIC,
New_vaccinations NUMERIC,
RollingPeopleVaccinated NUMERIC
)
INSERT INTO #PercentPopulationVaccinated
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(BIGINT, NEW_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS RollingPeapleVaccinated
--, (RollingPeapleVaccinated/population)*100, error, we need to create a CTE or TEMP Table
FROM PortfolioProject..CovidDeath dea
JOIN PortfolioProject..CovidVacinations vac
ON
dea.date=vac.date
AND
dea.location=vac.location
WHERE dea.Continent IS NOT NULL
--ORDER BY 2,3
SELECT * , (RollingPeopleVaccinated/population)*100 AS PercantageOfTotalPeopleVacinated
FROM #PercentPopulationVaccinated

--CREATE VIEW

CREATE VIEW PercentPopulationVaccinated AS
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(BIGINT, NEW_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS RollingPeapleVaccinated
--, (RollingPeapleVaccinated/population)*100, error, we need to create a CTE or TEMP Table
FROM PortfolioProject..CovidDeath dea
JOIN PortfolioProject..CovidVacinations vac
ON
dea.date=vac.date
AND
dea.location=vac.location
WHERE dea.Continent IS NOT NULL

SELECT * FROM PercentPopulationVaccinated

--VIEW showlikelihood of dying if you contact civid in Egypt.
CREATE VIEW LikelyhoodOdDeathIfInfectedInEgypt AS
SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 AS DeathPercentage
FROM PortfolioProject.dbo.CovidDeath
WHERE location LIKE '%egypt%'
--Order by 1,2

--Create Viw  percentage of population in Egypt got covid.
CREATE VIEW PercentageOfPopGotCovidInEgypt AS
SELECT location, date, population, total_cases, (total_cases/population)*100 AS PercentOfPopulationInfected
FROM PortfolioProject.dbo.CovidDeath
WHERE location LIKE '%egypt%'
--ORDER BY 1,2

--Create View showing countries with highest infection rate compared to population.
CREATE VIEW CountriesWithHighestInfectionRateComparedToPopulation AS
SELECT location, population, MAX(total_cases) AS HighestInfectionCount, MAX((total_cases/population))*100 AS PercentOfPopulationInfected
FROM PortfolioProject.dbo.CovidDeath
GROUP BY location, population
--ORDER BY 4 DESC

--Create a view of the highest infection by Continent
CREATE VIEW HighestContinentInfection AS
SELECT location, MAX(CAST(Total_deaths AS INT)) AS TotalDeathCount
FROM PortfolioProject.dbo.CovidDeath
WHERE continent IS NULL
GROUP BY location
--ORDER BY 2 DESC

-- Create a view of Total Numbers
CREATE VIEW GlobalNumbers AS
SELECT SUM(new_cases) AS SumOfNewCases, SUM(CAST(new_deaths AS INT)) AS SumofNewDeaths, SUM(CAST(new_deaths AS INT))/SUM(new_cases)*100 AS PercentageOfDeathPerCase
FROM PortfolioProject.dbo.CovidDeath
WHERE continent IS NOT NULL AND new_cases <> 0
--ORDER BY 2 DESC

-- Create view for Looking at Total Population VS Vacinations
CREATE VIEW TotalPopVsVaccination AS
SELECT dea.continent, dea.location,dea.date,population, vac.new_vaccinations
, SUM(CONVERT(bigint, vac.new_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date)
 AS RollingPeopleVacinated
-- , (RollingPeopleVacinated/Population)*100
FROM PortfolioProject.dbo.CovidDeath dea
JOIN PortfolioProject.dbo.CovidVacinations vac
ON dea.location = vac.location
AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
--ORDER BY 2,3


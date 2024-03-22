
----Selecting Data to use

SELECT Location, date, total_cases,new_cases,total_deaths,population

FROM local-sprite-417620.Portfolio_project.Covid_Deaths

ORDER BY Location, date

-- Looking at Total Cases vs Total Deaths

SELECT Location, date, total_cases,total_deaths,(total_deaths/total_cases) * 100 AS Death_Percentage

FROM local-sprite-417620.Portfolio_project.Covid_Deaths

WHERE Location like '%States%'
ORDER BY Location, date



--Looking at Total Cases vs Population
--Showing Percentage of Population contracting Covid

SELECT Location, date,Population, total_cases,(total_cases/population) * 100 AS Infection_population_percentage

FROM local-sprite-417620.Portfolio_project.Covid_Deaths

WHERE Location like '%States%'
ORDER BY Location, date


--Looking at Countries with Highest Infection rate compared to Population

SELECT Location,Population, MAX(total_cases) as Highest_Infection_Count,MAX((total_cases/Population)) * 100 AS Percent_Population_infected

FROM local-sprite-417620.Portfolio_project.Covid_Deaths

GROUP BY Location, Population
ORDER BY Percent_Population_infected DESC

--Showing Countries with Highest Death Count per Population

SELECT Location, MAX(CAST(Total_deaths as int)) as Total_Death_Count

FROM local-sprite-417620.Portfolio_project.Covid_Deaths
WHERE continent is not null
GROUP BY Location
ORDER BY Total_Death_count DESC

--Showing continent with Highest Death Count per Population

SELECT continent, MAX(CAST(Total_deaths as int)) as Total_Death_Count

FROM local-sprite-417620.Portfolio_project.Covid_Deaths
WHERE continent is not null
GROUP BY continent
ORDER BY Total_Death_count DESC


-- Global Numbers

SELECT  date,SUM(new_cases) as total_cases,SUM(cast(new_deaths as int)) as total_deaths, SUM(Cast(new_deaths as int)) / SUM(new_cases) * 100  Death_Percentage_Global

FROM local-sprite-417620.Portfolio_project.Covid_Deaths

WHERE continent is not null
GROUP BY DATE
ORDER BY 1,2

--TOTAL POPULATION Vs. Vaccinations

SELECT dea.continent,dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(CAST(vac.new_vaccinations as int)) OVER (PARTITION BY dea.location ORDER BY dea.location,dea.date) as Rolling_total_vaccinated

FROM local-sprite-417620.Portfolio_project.Covid_Deaths AS dea
JOIN local-sprite-417620.Portfolio_project.Covid_Vaccinations AS vac
ON dea.location = vac.location 
AND dea.date = vac.date
WHERE dea.continent is not null
ORDER BY 2,3

-----USING CTE


WITH PopvsVac

 (Continent,Location,new_vaccinations,Date,Population,Rolling_total_vaccinated)

 AS
(
SELECT dea.continent,dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(CAST(vac.new_vaccinations as int)) OVER (PARTITION BY dea.location ORDER BY dea.location,dea.date) as Rolling_total_vaccinated

FROM local-sprite-417620.Portfolio_project.Covid_Deaths AS dea
JOIN local-sprite-417620.Portfolio_project.Covid_Vaccinations AS vac
ON dea.location = vac.location 
AND dea.date = vac.date
WHERE dea.continent is not null
)
  SELECT * 
  FROM PopvsVac

  ---TEMP TABLE

 DROP Table if exists #PercentPopulationVaccinated
Create Table #PercentPopulationVaccinated
(
Continent nvarchar(255),
Location nvarchar(255),
Date datetime,
Population numeric,
New_vaccinations numeric,
RollingPeopleVaccinated numeric
)

Insert into #PercentPopulationVaccinated
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
--where dea.continent is not null 
--order by 2,3

Select *, (RollingPeopleVaccinated/Population)*100
From #PercentPopulationVaccinated


--CREATING VIEW to store

Create View PercentPopulationVaccinated as
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 



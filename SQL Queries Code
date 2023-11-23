


Select Location, date, total_cases,new_cases, total_deaths, population
From PortfolioProject..CovidDeaths
Order by 1,2

--Looking at Total Cases vs Population
--Shows what percentage of population got covid

Select Location, date,population, total_cases, (total_cases/population)*100 as PercentPopulationInfected
From PortfolioProject..CovidDeaths
Where location like '%states%'
Order by 1,2

--Looking at countries with highest infection rate compared to population

Select Location,population,Max(total_cases) as HighestInfectionCount, Max(total_cases/population)*100 as PercentPopulationInfected
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Group by Location, population
Order by PercentPopulationInfected desc

--Showing Countries with highest death count per population

Select Location,Max(cast(Total_deaths as int)) as TotalDeathCount
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Where continent is Not null
Group by Location
Order by TotalDeathCount desc

--Let's break things down by continient 
--Showing continents with highest death count per population


Select continent,Max(cast(Total_deaths as int)) as TotalDeathCount
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Where continent is Not null
Group by continent
Order by TotalDeathCount desc

--Global Numbers

Select date, SUM(new_cases) as total_cases,SUM(cast(new_deaths as int)) as total_deaths, Sum(cast(new_deaths as int))/Sum(New_Cases)*100 as DeathPercentage
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Where continent is not null
Group By date
Order by 1,2

--Looking at total population vs vaccinations
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
Sum(cast(vac.new_vaccinations as int))Over (Partition by dea.Location Order by dea.location,dea.Date) as RollingPeopleVaccinateed
from PortfolioProject..CovidDeaths dea
Join Portfolioproject..CovidVaccinations vac
On dea.location = vac.location
and dea.date = vac.date
Where dea.continent is not null
order by 1,2,3


--Use CTE

With PopvsVax (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated)
as
(
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
Sum(cast(vac.new_vaccinations as int))Over (Partition by dea.Location Order by dea.location,dea.Date) as RollingPeopleVaccinated
from PortfolioProject..CovidDeaths dea
Join Portfolioproject..CovidVaccinations vac
On dea.location = vac.location
and dea.date = vac.date
Where dea.continent is not null
--order by 1,2,3
)

Select * ,(RollingPeopleVaccinated/Population)*100
From PopvsVax

--Temp Table 
Drop Table if exists #PercentPopulationVaccinated
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
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
Sum(cast(vac.new_vaccinations as int))Over (Partition by dea.Location Order by dea.location,dea.Date) as RollingPeopleVaccinated
from PortfolioProject..CovidDeaths dea
Join Portfolioproject..CovidVaccinations vac
On dea.location = vac.location
and dea.date = vac.date
--Where dea.continent is not null
--order by 1,2,3


Select * ,(RollingPeopleVaccinated/Population)*100
From #PercentPopulationVaccinated

--Creating  views to store data for later visualizations

Create View PercentPopulationsVaccinated as 
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
Sum(cast(vac.new_vaccinations as int))Over (Partition by dea.Location Order by dea.location,dea.Date) as RollingPeopleVaccinated
from PortfolioProject..CovidDeaths dea
Join Portfolioproject..CovidVaccinations vac
On dea.location = vac.location
and dea.date = vac.date
Where dea.continent is not null
--order by 1,2,3








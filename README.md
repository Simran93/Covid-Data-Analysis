## Global COVID-19 Data Analysis: SQL queries in SQL Server for efficient exploration and analysis of worldwide COVID-19 deaths and vaccination data.


## Overview

The data, collected from Our World in Data, is organized into two Excel files: CovidDeaths.xlsx and CovidVaccinations.xlsx. These files are imported into a SQL Server database, where various SQL queries are executed for in-depth analysis. The accompanying code files provide transparency and facilitate replication of the analyses.

## Contents

### Data Collection

- Raw data sourced from Our World in Data.
- Segregated into two Excel files: CovidDeaths.xlsx and CovidVaccinations.xlsx.

### SQL Server Import

- Data imported into SQL Server for efficient querying and analysis.

### Code Files

- SQL queries performed on the imported data, enhancing its analytical potential.
- Code files are provided in the SQLQueries/ folder for reference and replication.

### File Structure

- CovidDeaths.xlsx: Excel file containing COVID-19 death data.
- CovidVaccinations.xlsx: Excel file containing COVID-19 vaccination data.
- Folder(Create Portfolio Projects) containing SQL code files for various analyses.

## Usage

1. **Data Exploration:**
   - Explore the raw data in the Excel files for insights.

2. **SQL Analysis:**
   - Execute SQL queries from the SQLQueries/ folder on the imported data.

3. **Customization:**
   - Modify the SQL queries or Excel files to suit specific analytical needs.

**Data source:** Our World in Data.

SQL QUERIES: 



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








# SQL
-- Data Cleaning 
-- first thing we want to do is create a staging table.
-- This is the one we will work in and clean the data. 
-- We want a table with the raw data in case something happens.

select * from layoffs;

-- 1. check for duplicates and remove any
-- 2. standardize data and fix errors
-- 3. Look at null values and see what 
-- 4. remove any columns and rows that are not necessary - few ways

 
 -- We need to really look at every single row to be accurate

-- these are our real duplicates 
 create table layoffs1
 like layoffs;
  
 insert layoffs1
 select * from layoffs;
 
  -- We need to really look at every single row to be accurate

-- these are our real duplicates 
 
 select *, row_number() over(partition by  company,location,industry,total_laid_off,
 percentage_laid_off, date, stage,funds_raised)
 from layoffs1; 
 
 with duplicate_cte as 
 (
 select *, row_number() over(partition by country, company,location,industry,total_laid_off,
 percentage_laid_off, 'date', stage,funds_raised) as row_num
 from layoffs1
 )
 select * from duplicate_cte
 where row_num > 1;

-- run query to find duplicate rows

select company,country,location,industry,total_laid_off,
 percentage_laid_off, 'date', stage,funds_raised, count(company) from
layoffs1
group by company,country, location,industry,total_laid_off,
 percentage_laid_off, 'date', stage,funds_raised
 having count(country) > 1;
 
 -- So there isn't any duplicates
 
-- 2.Standardizing Data
  
  Select distinct (company)
  from layoffs1;
  
 Select company, trim(company)
  from layoffs1;
   Select country, trim(country)
  from layoffs1;
  
update layoffs1
set company = trim(company);

 update layoffs1
 set country = trim(country);
 
  Select distinct (industry)
  from layoffs1
  order by 1;
  
  
  UPDATE layoffs1
  SET industry = 'CryptoCurrency'
  where industry like 'crypto';
  
  Select distinct (Location)
  from layoffs1
  order by 1;
   
Update layoffs1
set location = 'Dusseldorf'
where location like 'D%s%s%e%l%';


-- Let's also fix the date columns:

SELECT *
FROM world_layoffs.layoffs1;

-- we can use str to date to update this field
UPDATE layoffs1
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

-- now we can convert the data type properly

ALTER TABLE layoffs_staging2
MODIFY COLUMN `date` DATE;

-- 3. Look at Null Values or Blank Values

-- the null values in total_laid_off, percentage_laid_off, and funds_raised_millions all look normal. I don't think I want to change that
-- I like having them null because it makes it easier for calculations during the EDA phase

-- so there isn't anything I want to change with the null values

-- 4. remove any columns and rows we need to

SELECT *
FROM world_layoffs.layoffs1
WHERE total_laid_off IS NULL;

-- NO NULL VALUES
 

SELECT *
FROM world_layoffs.layoffs1
WHERE total_laid_off IS NULL
AND percentage_laid_off IS  NULL;

--   No null values 
-- Check for blank values
 
 select * from layoffs1
 where industry = '';
 
 update layoffs1
 set industry = 'Tech'
 where industry like '';
 
 select * from layoffs1
 where total_laid_off = '';

update layoffs1
set total_laid_off = null
where total_laid_off like '';

select * from layoffs1
where total_laid_off is null;


-- Done 

 
 

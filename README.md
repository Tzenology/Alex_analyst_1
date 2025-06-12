# 🧹 Data Cleaning Project (Guided by Alex The Analyst)

We are cleaning data from a dataset containing information on company layoffs. This tutorial is part of the **Data Analysis Bootcamp** led by [Alex the Analyst on YouTube](https://www.youtube.com/watch?v=4UltKCnnnTA&t=2940s).


### 🎯 Objectives

The aim of this project is to learn how to:

- Remove duplicates from a dataset
- Standardize and clean data
- Handle null and blank values
- Drop unnecessary rows and columns


### 🔧 The Cleaning Process

#### 1️⃣ Create a Staging Table

To avoid altering raw data directly, I created a **staging table** called `layoffs_staging` to perform all transformations safely.


#### 2️⃣ Remove Duplicates

- Created a `ROW_NUMBER()` partition over all columns to detect duplicates.

```sql
WITH duplicate_cte AS (
  SELECT *,
         ROW_NUMBER() OVER (
           PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions
           ORDER BY company
         ) AS row_num
  FROM layoffs_staging
)
DELETE FROM duplicate_cte
WHERE row_num > 1;
```


#### 3️⃣ Standardizing Data

I created a second staging table `layoffs_staging2` for this phase.

##### a. Remove White Space

Trimmed unnecessary white spaces from the `company` column:

```sql
SELECT company, TRIM(company)
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET company = TRIM(company);
```

##### b. Correct Misspelled or Inconsistent Data

Cleaned up inconsistent country names like `'United States.'`:

```sql
SELECT DISTINCT country
FROM layoffs_staging2
ORDER BY 1;

SELECT DISTINCT country, TRIM(TRAILING '.' FROM country)
FROM layoffs_staging2
WHERE country LIKE 'United States%';

UPDATE layoffs_staging2
SET country = TRIM(TRAILING '.' FROM country)
WHERE country LIKE 'United States%';
```

##### c. Convert Date Format

The `date` column was stored as text, so I converted it to date format:

```sql
SELECT `date`,
       STR_TO_DATE(`date`, '%m/%d/%Y')
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');
```


#### 4️⃣ Handle Null and Blank Values

- Identified and replaced blank values in the `industry` column with `NULL`.
- Filled missing `industry` values using a self join based on company names.
- Deleted rows where both `total_laid_off` and `percentage_laid_off` were null (no way to recover this data).


#### 5️⃣ Drop Temporary Columns

Finally, dropped the `row_num` column used to identify duplicates:

```sql
ALTER TABLE layoffs_staging2
DROP COLUMN row_num;
```


### ✅ Outcome

By the end of this project, I had successfully cleaned a messy dataset by applying key data cleaning techniques using SQL. This has strengthened my understanding of how to handle real-world data problems effectively.


### ⚠️ Challenges Faced

1. **Self Join on Blank Cells Failed:**  
   My first attempt at using a self join to fill missing values in the `industry` column failed because I tried to match against blank strings instead of `NULL`. I realized I had to convert blank values to `NULL` before attempting a self join.

2. **Incorrect Representation of Blank Values:**  
   Initially, I used `(' ')` (with a space between the quotes) when checking for blanks, which returned incorrect results. The correct representation was `('')` with no space. This mistake delayed progress and taught me to be precise when dealing with string values in SQL.



















  

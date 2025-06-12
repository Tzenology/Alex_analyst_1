# Data Cleaning led by Alex The Analyst

We are cleaning data from a dataset containing information on company layoffs. This tutorial is led by Alex the Analyst on Yotube under the Data Analysis Bootcamp.
(https://www.youtube.com/watch?v=4UltKCnnnTA&t=2940s)


The aim is to learn how to 
- Remove duplicates in a database
- Standardize the data
- Work with Null and Blank values
- Remove unnecessary rows and columns

### The Process

I started off with creating a staging table which I will use for making all changes. It is best practice to keep the row data unchanged just incase mistakes happen. I named this staging table `layoffs_staging`.

#### 1. Remove Duplicates

- I created a partition with a duplicate cte for all the columns in the table and named it `row_num`. This row will indicate which which rows have more than 1 entry.
- Then I deleted all rows with more than 1 entry from the duplicate cte.

#### 2. Standardizing Data

- I created another duplicate table which i will use for everything I want to delete. This table is named `layoffs_staging2`.

##### 2a. Remove White Space
-  I assesssed the presentation of data in the table and realized that some data was not properly aligned in the table. There were unnecessary white spaces in the `company` column.
- To tackle this, I trimmed the columnn using this syntax

    ```
     SELECT company, TRIM(company)
    FROM layoffs_staging2;
    
    UPDATE layoffs_staging2
    SET company = TRIM(company);
    ```


##### 2b. Correct Misspelled Data

- I assessed data in all columns and realized  that there was duplicated information due to misspelled data or punctuation. I corrected this using the following syntax

  ```
    SELECT DISTINCT COUNTRY
    FROM layoffs_staging2
    ORDER BY 1;

    SELECT *
    FROM layoffs_staging2;

    SELECT DISTINCT *
    FROM layoffs_staging2
    WHERE country LIKE 'United States%'
    ORDER BY 1;

    SELECT DISTINCT country, TRIM(TRAILING '.' FROM country)
    FROM layoffs_staging2
    WHERE country LIKE 'United States%';

    UPDATE layoffs_staging2
    SET country = TRIM(TRAILING '.' FROM country)
    WHERE country LIKE 'United States%';
  ```

##### 2c. Change Date from text to Timeformat
- In the original dataset, the `date` column was set as a text and not time formart and i changed that using this syntax:
 ```
SELECT `date`,
STR_TO_DATE(`date`, '%m/%d/%Y')
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');
```

#### 3. Working with Null and blank values

























  

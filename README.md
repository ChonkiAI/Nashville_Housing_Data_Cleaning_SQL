# 🏠 Nashville Housing Data Cleaning in SQL

## 📌 Project Overview
This project demonstrates **end-to-end SQL data cleaning** techniques applied to the Nashville Housing dataset.  
The goal was to prepare the dataset for further analysis by **standardizing formats, handling missing values, splitting fields, removing duplicates, and optimizing the schema**.  
All transformations were performed directly in **Microsoft SQL Server** using efficient SQL queries.

**Skills Demonstrated:**
- SQL Data Cleaning & Transformation
- Data Standardization
- Handling Missing Data
- String Manipulation Functions
- Using CTEs for Duplicate Removal
- ALTER TABLE & Schema Optimization

---

## 🗂 [Dataset](https://github.com/ChonkiAI/Nashville_Housing_Data_Cleaning_SQL/blob/main/Nashville%20Housing%20Data.xlsx)
The [dataset](https://github.com/ChonkiAI/Nashville_Housing_Data_Cleaning_SQL/blob/main/Nashville%20Housing%20Data.xlsx) contains Nashville real estate transaction records with details like property addresses, owner information, sale prices, and more.

## 📂 Dataset Preview

| UniqueID | ParcelID         | LandUse       | PropertyAddress                           | SaleDate           | SalePrice | LegalReference       | SoldAsVacant | OwnerName                                | OwnerAddress                                     | Acreage | TaxDistrict                | LandValue | BuildingValue | TotalValue | YearBuilt | Bedrooms | FullBath | HalfBath |
|----------|------------------|---------------|--------------------------------------------|--------------------|-----------|----------------------|--------------|-------------------------------------------|--------------------------------------------------|---------|----------------------------|-----------|--------------|------------|-----------|----------|----------|----------|
| 2045     | 007 00 0 125.00  | SINGLE FAMILY | 1808 FOX CHASE DR, GOODLETTSVILLE          | April 9, 2013      | 240000    | 20130412-0036474     | No           | FRAZIER, CYRENTHA LYNETTE                 | 1808 FOX CHASE DR, GOODLETTSVILLE, TN            | 2.3     | GENERAL SERVICES DISTRICT  | 50000     | 168200       | 235700     | 1986      | 3        | 3        | 0        |
| 16918    | 007 00 0 130.00  | SINGLE FAMILY | 1832 FOX CHASE DR, GOODLETTSVILLE          | June 10, 2014      | 366000    | 20140619-0053768     | No           | BONER, CHARLES & LESLIE                   | 1832 FOX CHASE DR, GOODLETTSVILLE, TN            | 3.5     | GENERAL SERVICES DISTRICT  | 50000     | 264100       | 319000     | 1998      | 3        | 3        | 2        |
| 54582    | 007 00 0 138.00  | SINGLE FAMILY | 1864 FOX CHASE DR, GOODLETTSVILLE          | September 26, 2016 | 435000    | 20160927-0101718     | No           | WILSON, JAMES E. & JOANNE                  | 1864 FOX CHASE DR, GOODLETTSVILLE, TN            | 2.9     | GENERAL SERVICES DISTRICT  | 50000     | 216200       | 298000     | 1987      | 4        | 3        | 0        |
| 43070    | 007 00 0 143.00  | SINGLE FAMILY | 1853 FOX CHASE DR, GOODLETTSVILLE          | January 29, 2016   | 255000    | 20160129-0008913     | No           | BAKER, JAY K. & SUSAN E.                   | 1853 FOX CHASE DR, GOODLETTSVILLE, TN            | 2.6     | GENERAL SERVICES DISTRICT  | 50000     | 147300       | 197300     | 1985      | 3        | 3        | 0        |
| 22714    | 007 00 0 149.00  | SINGLE FAMILY | 1829 FOX CHASE DR, GOODLETTSVILLE          | October 10, 2014   | 278000    | 20141015-0095255     | No           | POST, CHRISTOPHER M. & SAMANTHA C.         | 1829 FOX CHASE DR, GOODLETTSVILLE, TN            | 2       | GENERAL SERVICES DISTRICT  | 50000     | 152300       | 202300     | 1984      | 4        | 3        | 0        |
| 18367    | 007 00 0 151.00  | SINGLE FAMILY | 1821 FOX CHASE DR, GOODLETTSVILLE          | July 16, 2014      | 267000    | 20140718-0063802     | No           | FIELDS, KAREN L. & BRENT A.                | 1821 FOX CHASE DR, GOODLETTSVILLE, TN            | 2       | GENERAL SERVICES DISTRICT  | 50000     | 190400       | 259800     | 1980      | 3        | 3        | 0        |
| 19804    | 007 14 0 002.00  | SINGLE FAMILY | 2005 SADIE LN, GOODLETTSVILLE              | August 28, 2014    | 171000    | 20140903-0080214     | No           | HINTON, MICHAEL R. & CYNTHIA M. MOORE      | 2005 SADIE LN, GOODLETTSVILLE, TN                | 1.03    | GENERAL SERVICES DISTRICT  | 40000     | 137900       | 177900     | 1976      | 3        | 2        | 0        |
| 54583    | 007 14 0 024.00  | SINGLE FAMILY | 1917 GRACELAND DR, GOODLETTSVILLE          | September 27, 2016 | 262000    | 20161005-0105441     | No           | BAILOR, DARRELL & TAMMY                    | 1917 GRACELAND DR, GOODLETTSVILLE, TN            | 1.03    | GENERAL SERVICES DISTRICT  | 40000     | 157900       | 197900     | 1978      | 3        | 2        | 0        |
| 36500    | 007 14 0 026.00  | SINGLE FAMILY | 1428 SPRINGFIELD HWY, GOODLETTSVILLE       | August 14, 2015    | 285000    | 20150819-0083440     | No           | ROBERTS, MISTY L. & ROBERT M.              | 1428 SPRINGFIELD HWY, GOODLETTSVILLE, TN         | 1.67    | GENERAL SERVICES DISTRICT  | 45400     | 176900       | 222300     | 2000      | 3        | 2        | 1        |
| 19805    | 007 14 0 034.00  | SINGLE FAMILY | 1420 SPRINGFIELD HWY, GOODLETTSVILLE       | August 29, 2014    | 340000    | 20140909-0082348     | No           | LEE, JEFFREY & NANCY                       | 1420 SPRINGFIELD HWY, GOODLETTSVILLE, TN         | 1.3     | GENERAL SERVICES DISTRICT  | 40000     | 179600       | 219600     | 1995      | 5        | 3        | 0        |


---

## 🛠 Cleaning Steps & Queries

### 1️⃣ Standardizing Date Formats
**Objective:** Ensure all date values are in a consistent `YYYY-MM-DD` format.
Example Case:

SaleDate (Original)	SaleDateConverted
05-12-2014 00:00:00	2014-05-12
8/15/2013 00:00	2013-08-15
```
ALTER TABLE NashvilleHousing
ADD SaleDateConverted DATE;

UPDATE NashvilleHousing
SET SaleDateConverted = CONVERT(Date, SaleDate);
```
### 2️⃣ Populating Missing Property Addresses
**Objective:** Fill missing PropertyAddress fields using other records with the same ParcelID.
```
UPDATE a
SET PropertyAddress = ISNULL(a.PropertyAddress, b.PropertyAddress)
FROM PortfolioProject.dbo.NashvilleHousing a
JOIN PortfolioProject.dbo.NashvilleHousing b
  ON a.ParcelID = b.ParcelID
 AND a.[UniqueID ] <> b.[UniqueID ]
WHERE a.PropertyAddress IS NULL;
```
### 3️⃣ Splitting Address into Components
**Objective:** Break full address into separate columns for Address, City, State.
```
ALTER TABLE NashvilleHousing ADD PropertySplitAddress NVARCHAR(255);
UPDATE NashvilleHousing
SET PropertySplitAddress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress) - 1);

ALTER TABLE NashvilleHousing ADD PropertySplitCity NVARCHAR(255);
UPDATE NashvilleHousing
SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress) + 1, LEN(PropertyAddress));
```
### 4️⃣ Standardizing "Sold as Vacant" Values
**Objective:** Replace 'Y' and 'N' with 'Yes' and 'No' for clarity.
```
UPDATE NashvilleHousing
SET SoldAsVacant = CASE
    WHEN SoldAsVacant = 'Y' THEN 'Yes'
    WHEN SoldAsVacant = 'N' THEN 'No'
    ELSE SoldAsVacant
END;
```
### 5️⃣ Removing Duplicate Records
**Objective:** Identify and remove duplicate rows based on key fields.
```
WITH RowNumCTE AS (
    SELECT *,
           ROW_NUMBER() OVER (
               PARTITION BY ParcelID, PropertyAddress, SalePrice, SaleDate, LegalReference
               ORDER BY UniqueID
           ) row_num
    FROM PortfolioProject.dbo.NashvilleHousing
)
DELETE
FROM RowNumCTE
WHERE row_num > 1;
```
### 6️⃣ Dropping Unused Columns
**Objective:** Remove columns no longer needed after cleaning.
```
ALTER TABLE PortfolioProject.dbo.NashvilleHousing
DROP COLUMN OwnerAddress, TaxDistrict, PropertyAddress, SaleDate;
```
---
## 📊 Query Effects: Before vs After

| Step | Transformation | Before | After |
|------|----------------|--------|-------|
| **1. Standardizing Date Format** | Convert `SaleDate` to `YYYY-MM-DD` | `05-12-2014 00:00:00` | `2014-05-12` |
| **2. Populating Missing Property Addresses** | Fill null `PropertyAddress` using matching `ParcelID` | `NULL` | `742 Evergreen Terrace` |
| **3. Splitting Address into Components** | Break into `PropertySplitAddress`, `PropertySplitCity`, `OwnerSplitAddress`, `OwnerSplitCity`, `OwnerSplitState` | `123 Main St, Nashville` / `456 Oak Rd, Franklin, TN` | `123 Main St` / `Nashville` / `456 Oak Rd` / `Franklin` / `TN` |
| **4. Standardizing "Sold as Vacant" Values** | Replace `Y` / `N` with `Yes` / `No` | `Y` | `Yes` |
| **5. Removing Duplicate Records** | Remove repeated rows with same key fields | Two identical `ParcelID 123456` records | Single `ParcelID 123456` record |
| **6. Dropping Unused Columns** | Remove `OwnerAddress`, `TaxDistrict`, `PropertyAddress`, `SaleDate` | Table had unnecessary columns | Table contains only cleaned, relevant columns |

---
### 📈 Key Outcomes
**1: Fully cleaned dataset ready for analysis.**

**2: Reduced redundancy by removing duplicates.**

**3: Improved readability by standardizing formats and splitting columns.**

**4: Schema optimized for downstream BI or data science workflows.**


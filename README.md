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


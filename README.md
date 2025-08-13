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
### 📈 Key Outcomes
**1: Fully cleaned dataset ready for analysis.**

**2: Reduced redundancy by removing duplicates.**

**3: Improved readability by standardizing formats and splitting columns.**

**4: Schema optimized for downstream BI or data science workflows.**


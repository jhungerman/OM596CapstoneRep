# Initial Notes
- Data pull consists of 3 sheets
- Property Data has 15923 records
- Sub Area Detail has 53713 records
- Sales Data has 22152 records over 2 years

# Data Preparation

## Property Data Prep
- Data contains duplicate account number with separate cards. Need to determine best way to eliminate duplicates
- Accounts with multiple cards seem to be summed to the TotalAppraisal & GrossFInishedAre.
- Remove all records of vacant land
- Remove all records of yard items only
- Remove all commercial structures
- Remove all mobile / manufactured / modular housing
- Scan remaining accounts for duplicates on both account and card
- Remove record that is missing key information or 1 of the records if info matches
- Change remaining card numbers to 1
- Add variable concatenating Account number and card number
- Remove records where Year Built is 0
- Remove records where lot dimensions shows invalid data
- Move lot dimensions to end
- Scan for IRR and Missing. New variable LotType = "Normal," "Irregular," or "Missing"
- Remove IRR from LotDimensions
- Split LotDimensions using Text-to-Columns and 'X' as delimiter
- Count number of active dimensions 1-7
- Determined that some dimensions are marked 'M.' Follow-up with Bryce on whether this is Metric.
- Added column for Metric v English until Bryce response
	- Bryce responded that the 'M' is for manual calculation. Change variable to Manual v Automatic
- For comparison purposes I formatted everything as text. Add columns to convert lot dims to numeric for calculation
- Calculate average lot dim. If Missing then 0. Remove zeros.
- Apostrophes in some lot dimensions. Remove.
- Some lot dimensions were missing delimeters. Adjust.
- Handful of measurements had extended inch values. Convert to decimal.
- Re-run formulas
- There are several subdivision names with extra characters. I ran a pivot table to identify and correct.
- LotDimCount of 2 is actually a rectangle. Change all 2s to 4
- Calculate estimated square footage using the formula: area= (AvgDim^2*LotDimCount)/(4*tan(PI()/LotDimCount)) (Simmons, 2017)
- 1486 records with missing data. How do I handle this? Will determine after transferring to Sales Data sheet

## Sub Area Detail Prep
- Data is in a standard spreadsheet format with multiple rows per account. Need to format into database style
- Copy data to new sheet
- Create new variable AccCard to combine account number and card. This will key into other sheets
- Insert Pivot Table with AccCard as rows, SubArea as columns, sum Total Sketched Area & sum Total Finished Area as Detail
- Run formulas: =trim(B1)&"Sk" in cell B2, =trim(B1)&"Fin" in cell B3 for column names. Fill accross.
- Remove variables where sum= 0
- Remove "Sk" variables where sum "Sk" = sum "Fin"
- Replace all missing values with 0

## Sales Data Prep
- Sales Data is the primary sheet as it contains the sales price information.
- Remove all Sales Data records where the sales price is 0
- Remove all Sales Data records where Deed Type = MULTIPLE PARCEL
- For Prep work, added new Variable AccCard (Account + Card) to link Prop Data WS & Sub Area WS
- Created Access Database for linking sheets and creating main database sheet
	- Link to tables Property Data WS, Sales Data WS, and Sub Area WS
	- 1st query all variables from Sales Data and Property Data, inner join on AccCard as qry_PropToSales
	- 2nd query all variables from qry_PropToSales and Sub Area Data, inner join on SalesDataWS.AccCard and AccCard as qry_SubToSalesAndProp
	- Export data to Excel as FullVariableData
		- Review individual variables and use to formulate Create Table Query
			- SalesDataWS.AccCard:	Primary key. Keep this as AccCard. Remove the other 2 from other worksheets
			- AccountNumber:	Not needed, coded into AccCard (Three separate instances. None included)
			- CardNumber:		Not needed, coded into AccCard (Three separate instances. None included)
			- SaleDate:		Time of sale could be a factor. Include
			- SeqNumber:		Unsure of definition. **Follow-up with Bryce.** Include for now.
			- SalePrice:		Keep
			- LegalReference:	Transaction ID code. Exclude
			- Book:			Transaction ID code. Exclude
			- Page:			Transaction ID code. Exclude
			- NALCode:		Keep
			- DeedType:		Keep
			- ParcelID:		Not needed, superfluous ID field
			- PrimaryLandUse:	All "RS", will have no value, do not move
			- StreetNumber:		~~Likely will have no value, but keep for now~~
						Exclude
			- StreetName:		~~Likely will have no value, but keep for now~~
						Exclude
			- PropertyZip:		Keep
			- Subdivision:		Keep
			- TotalAppraisal:	Keep
			- CardLandUse:		All "RS", will have no value, do not move
			- CardAppraisal:	Holdover from having multiple cards. Data may be invalid. Exclude.
			- YearBuilt:		Keep
			- EffectiveYearBuilt:	This may have invalid data. Holdover from multiple cards. Exclude.
			- Block:		This variable is subdivision specific. No value. Exclude.
			- Lot:			This variable is subdivision specific. No value. Exclude.
			- Vacant_Improved	All "IMPROVED", exclude
			- BuildingTypeCode	Coded value of BuildingType. Exclude
			- BuildingType		Obscure description of property that has more to do with architecture. Exclude	
			- BuildingGrade		**Need better definition from Bryce.** Seems valuable. Keep.
			- StoryHeight		Keep
			- StoryHgtDescription	Just a description of StoryHeight. Exclude
			- Foundation		Exclude
			- RoofStructure		Exclude
			- RoofMaterial		Keep for now
			- ExteriorPrimeWall	Keep
			- Fireplaces		Keep
			- TotalGrossArea	Keep
			- FinishedArea		May have colinearity issues with Gross Area, but keep
			- CalcMethod		Likely not useful, but keep through the remainder of data cleansing
			- LotDimensions		Square footage all that is needed, exclude
			- LotType		Likely not useful, but keep through the remainder of data cleansing
			- LotDim (X:X)		Exclude
			- AvgDim		This was useful for calculating SqFt. Exclude now.
			- EstSF			Keep
			- SubArea Dims		Keep all
		- Create new data table and export to Excel
### Data Preparation Post Secondary Steps
- Found 1 record missing SalePrice. Remove record.
- Found 2 records missing PropertyZip. Remove records.
- 1206 records with missing subdivision.
	- 296 records have street names that correspond to unique subdivisions. Update records. Leaves 914 records without Sub
	- Produced list of streets, numbers, and subs. Scan list to see if missing subs can be identified by similar addresses
		- 679 remaining records with missing subdivision. Update to Street name. Proximity more important than actual subdivision
		- Move back to Data Exploration
- Created new variable AccCardSale to create unique id for each record since there are multiple sales on some properties
- 57 records with no street address. ~~Remove~~ This was due to a data conversion error between Excel & Access. Fixed.
	- 1 record remaining with no street address. Remove
- 9% data missing EstSF (1291 records). Ran table to calculate Mean and StdDev by Subdivision. 
	- Calculated 1 StdDev above and below mean
	- ~~Calculated random number between StdDev min and max as estimate. Any <0 were calculated again between mean and 1 StdDev above~~
		- This method, while seemingly statistically valid, would still overweight missing values to the calculated number
		- Revert data
	- Since the size of a yard is often affected by the size of the house, I calculated an Estimated sqFt using Total Area
		- (Est SF for the subdivision)*(size of dwelling/average size of dwelling for sub)
		- This created some instances where the yard was greater than 1 standard deviation of the mean SF for the sub
			- For these I limited the max to the avg + 1 standard deviation
	- 657 remining records where there was no square footage estimate for the given subdivision
		- I ran the same process using Zip as I had using subdivision.
	- 4 records remaining with no EstSF. Delete from table
- Subdivision has too many levels. How do I address?
	- Find levels based on appraised value and variance / standard deviation
		- Needs to be done on full property data not just the sales subset
		- Calculated average appraised value for all properties as Mid Value, 1 stdev below as Low Value, and 1 stdev above as High Value
			- Low = $88,593
			- Mid = $165,099
			- High = $241,605
		- Calculated absolute Z score for average appraised value by subdivision and used to separate variability levels
			- Low = 0.08
			- Mid = 1.0
			- High = 1.93
		- Created new variable ValVar to concatenate value bin and variability bin
		- Added 9 dummy, binary variables for the 9 ValVar categories for future regression
			- Only 6 categories contained data
				- Low value, high variability
				- Low value, mid variability
				- Mid value, low variability
				- Mid value, mid variability
				- High value, mid variability
				- High value, high variability
			- Removed low val/low var, mid val/high var, high val/low var
		- Not enough separation in midrange
			- Set 2 new levels on value (1/3 stdev)
				- Mid Low = $139,852
				- Mid High = $190,346
			- ~~Set 2 new levels on variability (1/3 stdev)~~
				- ~~Mid Low = 0.70~~
				- ~~Mid High = 1.31~~
			- Could not get a more representative sample by splitting variability further
- StreetNum, StreetName, Subdivision not longer needed. Remove.
- Changed BuildingGrade to Ordinal from character
	- BELOW AVERAGE = -1
	- AVERAGE = 0
	- ABOVE AVERAGE = 1


## Combined Data Set Prep **(AssessmentDataSetFromAccess.xlsx)**
	- Convert SaleDate to Excel date code
	- Used SAS to convert to SAS data table

# Data Exploration
- ~~Initial data set has 57 variables and 14192 observations when moved to Enterprise Miner 14.1~~
- Data has 67 variables and 11845 observations
- Reviewed data and determined further data cleansing was necessary. Move back.
- Ran 1st regression analysis
	- R-SQ 36%, remove records NAL Code = Disqualified
- Ran 2nd analysis
	- Too complex. Need to remove records in DeedType other than QUALIFIED/ACCEPTED, remove NALCode and Deed Type Columns
	- Review SubArea variables and remove those with no usable data (found during Minitab exploration)










References:

Simmons, B. (2017, July 19). Area of a Regular Polygon. Retrieved September 07, 2017, from http://www.mathwords.com/a/area_regular_polygon.htm
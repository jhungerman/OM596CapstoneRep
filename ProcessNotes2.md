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
- Bring over variables from Property Data
	- AccountNumber:	Not needed, coded into AccCard
	- CardNumber:		Not needed, coded into AccCard
	- ParcelID:		Not needed, superfluous ID field
	- PrimaryLandUse:	All "RS", will have no value, do not move






References:

Simmons, B. (2017, July 19). Area of a Regular Polygon. Retrieved September 07, 2017, from http://www.mathwords.com/a/area_regular_polygon.htm
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

## Sales Data Prep
- Remove all Sales Data records where the sales price is 0
- Remove all Sales Data records where Deed Type = MULTIPLE PARCEL
- For Prep work, added new Variable AccCard (Account + Card) to link Prop Data Sheet
- Brought over Vacant variable and Build Type variable from PropData to SalesData
- Remove all sales records for vacant land
- Remove all sales records for property where Building Type is Missing
- Remove records where Building Type is Commercial
- Remove all sales records for mobile/manufactured housing as not in our business scope

## Sub Area Detail Prep
- Remove all data where area = Null
- Need to transpose data, so that each row is a unique record
### Transpose Data
- Add variable AccCardSub to make each row unique
- Remove spaces from Sub Area names
- Use nested formulas to transpose data to new variable columns for sketched and finished area
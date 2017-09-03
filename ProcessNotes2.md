## Initial Notes
- Data pull consists of 3 sheets
- Property Data has 15923 records
- Sub Area Detail has 53713 records
- Sales Data has 22152 records over 2 years

## Data Preparation
- Remove all Sales Data records where the sales price is 0
- Remove all Sales Data records where Deed Type = MULTIPLE PARCEL
- For Prep work, added new Variable AccCard (Account + Card) to link Prop Data Sheet
- Brought over Vacant variable and Build Type variable from PropData to SalesData
- Remove all sales records for vacant land
- Remove all sales records for property where Building Type is Missing
- Remove records where Building Type is Commercial
- Remove all sales records for mobile/manufactured housing as not in our business scope
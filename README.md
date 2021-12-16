# Mapping Court of Appeals judges' schools

Final project for CJS Data and Databases class
* Jupyter notebooks are in the main repository
* [Data output](https://github.com/ilenapeng/judges-education-map/tree/main/data) - Data exported from and imported into the Jupyter notebooks 
* [Final map](https://github.com/ilenapeng/judges-education-map/tree/main/map)

## Process:
### [1. Scrape the education information for each judge](https://github.com/ilenapeng/judges-education-map/blob/main/01-scrape.ipynb)
* Scrape data from the Federal Judicial Center using BeautifulSoup and Selenium
* Webpages with all of the judges (past and present) from each circuit: https://<!--This is a comment-->fjc.gov/<!--This is, too-->history/courts/u.s.-court-appeals-{court-name}-judges
    * [Example from the D.C. Circuit](https://www.fjc.gov/history/courts/u.s.-court-appeals-district-columbia-circuit-justices-and-judges) 
* Loop through these webpages to retrieve links for each judge's biography: https://<!--This is a comment-->fjc.gov/<!--This is, too-->history/judges/{lastname-firstname-middlename}
    * [Example of a judge's biography page](https://www.fjc.gov/history/judges/knapp-martin-augustine)
* Find judge name using BeautifulSoup, use regex to scrape between "Education:" and "Professional" - this outputs one long string with the education
* Output: [01-scrape.csv](https://github.com/ilenapeng/judges-education-map/blob/main/data/01-scrape.csv)

### [2. Split the scraped output into multiple columns](https://github.com/ilenapeng/judges-education-map/blob/main/02-split.ipynb)
* Use regex to split the judges' names and educations into multiple columns
* Example: original string "Yale College, A.B., 1868Columbia Law School, LL.B.,1870" was split into the following columns
    * school1: "Yale College, A.B."
    * year1: 1868
    * school2: "Columbia Law School, LL.B."
    * year2: 1870
* Output: [02-split.csv](https://github.com/ilenapeng/judges-education-map/blob/main/data/02-split.csv)

### [3. Clean the outputs from the split strings](https://github.com/ilenapeng/judges-education-map/blob/main/03-clean.ipynb)
* Replace mentions of "Read law" with NaNs. These are indications of having a degree, but no actual school is listed (mostly for older judges)
* Use regex to remove degrees (B.A., Ph.D., LL.B., etc.)
    * Replaced schools that started with St. with St### to avoid accidentally splitting them in this process 
    * Also renamed schools with periods in their names, like Ohio Northern University, Warren G. Harding College of Law
* Did a bunch of renaming of schools for the following reasons:
    * Have "Harvard College," "Harvard School of Law" and "Harvard Business School" to all be "Harvard University"
    * Clean straggling punctuation
    * Align with our merge to get addresses for each school in Step 5
* Output: [03-clean.csv](https://github.com/ilenapeng/judges-education-map/blob/main/data/03-clean.csv)

### [4. Format the data into dataframes with counts for each schoool](https://github.com/ilenapeng/judges-education-map/blob/main/04-format.ipynb)
* Count number of times each school appeared overall, across all circuits, for the table that appears on the final map
* Count number of times each school appears in each circuit for the final map
* Outputs: [04-format.csv](https://github.com/ilenapeng/judges-education-map/blob/main/data/04-format.csv) and [04-format-table.csv](https://github.com/ilenapeng/judges-education-map/blob/main/data/04-format-table.csv)

### [5. Get addresses for each school](https://github.com/ilenapeng/judges-education-map/blob/main/05-address.ipynb)
* Data on schools downloaded from [the Database of Accredited Postsecondary Institutions and Programs](https://ope.ed.gov/dapip/#/download-data-files)
* Renamed schools to align with the address data
    * The data on judges' educations include state systems like University of Texas that have multiple locations, so defaulted to one of the schools in the system, like University of Texas at Austin
    * International schools had NaNs
    * Schools that had closed were replaced either with former addresses, if it could be found, or their city's City Hall
    * Also replaced the address for West Virginia State University, since the one provided in the DAPIP data geocodes incorrectly
* Removed extra columns, keeping only the name of the school, the count, the circuit and the address 
* Output: [05-address.csv](https://github.com/ilenapeng/judges-education-map/blob/main/data/05-address.csv)

### [6. Geocode the addresses](https://github.com/ilenapeng/judges-education-map/blob/main/06-geocode.ipynb)
* Used HERE Geocoder API to get addresses for each location 
* NaNs in the "address" column were automatically replaced with a latitude of 6.48812 and a longitude of 2.6138, these values were then replaced with NaNs
* Output: [06-geocode.csv](https://github.com/ilenapeng/judges-education-map/blob/main/data/06-geocode.csv)

### [7. Convert the data into a geojson](https://github.com/ilenapeng/judges-education-map/blob/main/07-geojson.ipynb)
* Create popup text (article), rollover text (headline), name (the school), names (group_name) and order (group_id) for the groups in the dropdown menu 
* Colors were set based on the circuit, circles were scaled by area
* Output: [07-geojson.js](https://github.com/ilenapeng/judges-education-map/blob/main/data/07-geojson.js) > Copy file to the final map folder as geo-data.js



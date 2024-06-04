# Content Preparation Script

Content Service provides content details for the Mobile App to access and play. The content metadata and source links (URLs) are stored in a database from which the Content Service provides the details.

To upload content details to the database, following is the process:
1. Get the content details in a CSV file
2. Modify the csv to add Youtube content ids
3. Run the content preparation script to add content metadata for contents on DIKSHA. It generates an output csv
4. Import the output csv to database

## Step 1: Get the content details in a CSV file
The content will need to be shared in the following format: [Click Here](https://raw.githubusercontent.com/Sunbird-AIAssistant/content-service/main/script/sample-csv/input-file.csv)

Make sure the input csv is in the required format

## Step 2: Modify the CSV to add Youtube content ids (please make sure if these steps are correct)
1. Add a new column as third column with name "identifier) (in the first row).
2. Add the following formula to all the rows in that column:
```
=IF(ISNUMBER(SEARCH("v=", B2)), MID(B2, SEARCH("v=", B2)+2, 11), IF(ISNUMBER(FIND(".be/", B2)), MID(B2, FIND(".be/", B2)+4, 11), IF(ISNUMBER(FIND("/shorts/", B2)), MID(B2, FIND("/shorts/", B2)+LEN("/shorts/"), 11), IF(ISNUMBER(FIND("youtu.be/", B2)), MID(B2, FIND("youtu.be/", B2)+LEN("youtu.be/"), 11), ""))))
```
3. The above will extract the youtube content id from the given URL and populate it in the column.

## Step 3: Run the content preparation script to add content metadata for contents on DIKSHA. It generates an output csv
1. Run the following command:
    - Go to the script root folder & run below command to build the script.
      
	```mvn clean install```
    - For executing:

	```
	mvn exec:java -Dexec.mainClass="com.script.main.App" -Dexec.args="<local path for input-file.csv> <local path for output-file.csv>"

	sample: 
	mvn exec:java -Dexec.mainClass="com.script.main.App" -Dexec.args="/Users/Documents/input-fiel.csv /Users/Documents/output-fiel.csv"
	```
3. The output will be in the following format: [Click Here](https://raw.githubusercontent.com/Sunbird-AIAssistant/content-service/main/script/sample-csv/output-file.csv)

## 4: Import the final CSV to database
Log in to the PostgreSQL instance for the contentdb database using the psql client as the admin user, and execute the following command to import data.
```
\copy djp_contents FROM '/path/to/csv/file.csv' DELIMITER ',' CSV HEADER;
UPDATE djp_contents SET status = 'Live';
```

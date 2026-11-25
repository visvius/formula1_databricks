Formula1 project

Source (Landing): 
- 6 data sources, placed in 'landing' volume
- Data: 
	csv- circuits, races
	json- constructors, drivers, results, sprints(multi-line)
- All tables stored as delta tables (which are paquet files with a transaction log)

Bronze:
- Imported using spark dataframe reader API into bronze with the addition of 2 ingestion metadata 
 columns (source file and ingestion timestamp) to each table

Silver:
- Changed column names to snake case to maintain uniformity across tables
- Dropped columns not needed for analytics like URL
- Deduplication

Gold:
- Changed data to 3 dimension and 1 fact table to better arrange them for analysis
- Removed the columns not needed currently for analysis including ingestion_timestamps and source_file
- Fact Table:
	- Merged (union) data of results and sprint (similar schema in silver for both)
	- 'session_type' specifies the race type and contains string value "RACE" or "SPRINT"
	- added columns 'is_win' and 'is_podium' 
- Dimention Table:
	- dim_races : Complete contextual info about races including necessary data about the circuits (race tracks)
	- dim_constructors : details about the constructors and their nationality (using the reference lookup table)
	- dim_drivers : details about drivers including their nationality
- ref_nationality_region : It acts as a lookup table for finding the driver's nationality_region using his nationaility

Jobs and Pipelines: 
- Created a job with individual tasks for each table
- added dependencies so next layer only executes after the previous layers
- added time trigger to execute these jobs at regular intervals (test file and table update triggers as well)
- job noticification also available for job success or failure (tested but not added)

SQL Editor: 
- Created a query to compare the standing of 2 races over the season
- Created a visualization for it and added it to a new dashboard



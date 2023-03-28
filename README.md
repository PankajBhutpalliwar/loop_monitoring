# Store Monitoring System

Loop monitors several restaurants in the US and needs to monitor if the store is online or not. All restaurants are supposed to be online during their business hours. Due to some unknown reasons, a store might go inactive for a few hours. Restaurant owners want to get a report of the how often this happened in the past.

1. You need two APIs 
    1. /trigger_report endpoint that will trigger report generation from the data provided (stored in DB)
        1. No input 
        2. Output - report_id (random string) 
        3. report_id will be used for polling the status of report completion
    2. /get_report endpoint that will return the status of the report or the csv
        1. Input - report_id
        2. Output
            - if report generation is not complete, return “Running” as the output
            - if report generation is complete, return “Complete” along with the CSV file with the schema described above.


# Datasource
We will have 3 sources of data 

1. We poll every store roughly every hour and have data about whether the store was active or not in a CSV.  The CSV has 3 columns (`store_id, timestamp_utc, status`) where status is active or inactive.  All timestamps are in **UTC**
    1. Data can be found in CSV format [here](https://drive.google.com/file/d/1UIx1hVJ7qt_6oQoGZgb8B3P2vd1FD025/view?usp=sharing)
2. We have the business hours of all the stores - schema of this data is `store_id, dayOfWeek(0=Monday, 6=Sunday), start_time_local, end_time_local`
    1. These times are in the **local time zone**
    2. If data is missing for a store, assume it is open 24*7
    3. Data can be found in CSV format [here](https://drive.google.com/file/d/1va1X3ydSh-0Rt1hsy2QSnHRA4w57PcXg/view?usp=sharing)
3. Timezone for the stores - schema is `store_id, timezone_str`
    1. If data is missing for a store, assume it is America/Chicago
    2. This is used so that data sources 1 and 2 can be compared against each other. 
    3. Data can be found in CSV format [here](https://drive.google.com/file/d/101P9quxHoMZMZCVWQ5o-shonk2lgK1-o/view?usp=sharing)

**_NOTE:_**  Data files cannot be pushed due to lfs issue 
     
         #### Files structure 
         
         ```
            data/
            
              ├── business_hours.csv
              
              ├── stores.csv
              
              └── timezones.csv
            ```

## System Requirements 

* The data sources are not static, and the system
should not precompute the answers. The system should keep updating the
data every hour. 
* The system should store the CSVs into a relevant
database and make API calls to get the data.

# Create conda virtual environment 
conda create -n yourenvname python=x.x anaconda

# Activate conda environment
conda activate yourenvname

## Installation 

To install the required packages, run the following command in the project directory:
```
    pip install -r requirements.txt
```

## Usage

To start the server, run the following command in the project directory:
```
    python run.py

```

The server will start running on http://localhost:5000

Note: Remember to add url prefix in the api like http://localhost:5000/api

## API Documentation

- ### /trigger_report

    This endpoint triggers the generation of a report from the data provided (stored in the database).

    * Request
    ```
    POST /api/trigger_report 
    ```
    * Response
    ```
        HTTP/1.1 200 OK
    Content-Type: application/json

    {
        'message': 'Success', 
        'error_code':200,
        "report_id": "random_string"
    }

    ```

- ### /get_report

    This endpoint returns the status of the report or the CSV.
    
    * Request
    ```
    GET /api/get_report?report_id=random_string 

    ```
    * Response
    ```
        HTTP/1.1 200 OK
        Content-Type: text/csv

        store_id, uptime_last_hour(in minutes), uptime_last_day(in hours), update_last_week(in hours), downtime_last_hour(in minutes), downtime_last_day(in hours), downtime_last_week(in hours)
        1, 60, 


    ```



"# loop_monitoring" 

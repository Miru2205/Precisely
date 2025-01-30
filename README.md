# Precisely Data Enrichment And Analysis

This project provides a set of tools to enrich datasets using the Precisely API. It includes functionality to fetch and process data from various Precisely datasets, including demographics, flood risk, coastal risk, property attributes, parcel data, and building information.

## Features

- Fetch demographic data (PsyteGeodemographics)
- Retrieve flood risk and coastal risk information
- Enhance property data with additional attributes
- Secure authentication handling with automatic token refresh
- Data sampling capabilities
- Progress tracking with tqdm

## Components

1. `demographicsDataPrecisely`: Handles API calls for demographic, flood risk, and coastal risk data.
2. `dataProcessorForDemographics`: Processes and combines the data retrieved from the API.
3. `propertyDataPrecisely`: Manages API calls for property, parcel, and building data.

## Prerequisites

- Python 3.x
- Required Python packages: pandas, requests, tqdm, requests, json, threading, base64

## Installation

1. Clone this repository:
   ```
   git clone https://github.com/rishikax/Precisely.git
   cd Precisely
   ```

2. Install the required packages:
   ```
   pip install pandas requests tqdm
   ```

## User Manual

Setting User environment:

1. MacOS/ Linux
```
python3 -m venv env
source env/bin/activate
```

2. For windows
```
python -m venv env
.\env\Scripts\activate
```

```pip install -r requirements.txt```

1. Downloading the Data for Property Attributes:
 Command Template:

Command Template:

Example 1:
```
python enhance_property_data.py --client_id YOUR_CLIENT_ID --client_secret YOUR_CLIENT_SECRET \
--file_path path/to/input.csv --output_path path/to/output.csv \
--start_row START_ROW --end_row END_ROW --sample_percentage SAMPLE_PERCENTAGE
```
Example 2:
```
python enhance_property_data.py --client_id abc123 --client_secret xyz789 \
--file_path data/filtered_data.csv --output_path data/enriched_data.csv \
--start_row 15000 --end_row 20000 --sample_percentage 100
```
Example 3:
```
python enhance_property_data.py --client_id YOUR_CLIENT_ID --client_secret YOUR_CLIENT_SECRET \
--file_path path/to/input.csv --output_path path/to/output.csv \
--start_row 1000 --end_row 2000 --sample_percentage 80
```

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `--client_id` | string | Yes | Precisely API Client ID. |
| `--client_secret` | string | Yes | Precisely API Client Secret. |
| `--file_path` | string | Yes | Path to the input CSV file containing the property data. |
| `--output_path` | string | Yes | Path where the enriched data will be saved. |
| `--start_row` | int | No | Starting row index for processing (default is 0). |
| `--end_row` | int | No | Ending row index for processing (default processes all rows from start). |
| `--sample_percentage` | int | No | Percentage of data to sample (default is 100%). |


For further analysis, please run throgh the ipynb notes to see the code, and analysis results with detailed explanation

## API Queries

The Precisely API is queried using GraphQL. The project includes several predefined queries for retrieving data:

1. **Psyte Geodemographics**
2. **Coastal Risk**
3. **Flood Risk**
4.  **Property Data**: getPropertyAttributesbyAddress, getParcelbyAddress, getBuildingbyAddress

These queries are sent to the Precisely API using the `get_response` method.

| Query Type | Description |
|------------|-------------|
| `psyteGeodemographics` | Retrieves demographic categories, census data, income, and property types. |
| `coastalRisk` | Retrieves information about the nearest coast and potential risk factors. |
| `floodRisk` | Retrieves flood zone and risk details, including FEMA flood zones. |
| `Property Data` | Retrieves property, building and parcel data based on address and PBKEY |

## Project Classes

### `demographicsDataPrecisely`

This class handles API token generation, token expiration management, and querying of demographic and risk data from Precisely.

| Method | Description |
|--------|-------------|
| `generate_auth_token()` | Retrieves a new authentication token from the Precisely API. |
| `refresh_token()` | Refreshes the token when expired and updates headers. |
| `process_dataframe(df)` | Processes a pandas DataFrame and retrieves data for each `PBKEY`. |
| `generate_psyteGeodemographics_query()` | Builds the GraphQL query for demographic data. |
| `generate_coastalRisk_query()` | Builds the GraphQL query for coastal risk data. |
| `generate_floodRisk_query()` | Builds the GraphQL query for flood risk data. |
| `get_response(query)` | Executes a query and retrieves results from Precisely API. |

### `dataProcessorForDemographics`

Processes the results from the API and combines them into a structured DataFrame.

| Method | Description |
|--------|-------------|
| `extract_data(result)` | Extracts psyte, coastal, and flood data from API responses. |
| `flatten_and_prefix(data, prefix)` | Flattens nested JSON data and adds a prefix to column names. |
| `process_single_result(result)` | Processes a single result and combines it into a DataFrame row. |
| `create_combined_dataframe()` | Combines all API results into a single DataFrame. |
| `save_to_csv(filename)` | Exports the combined DataFrame to a CSV file. |

### `propertyDataPrecisely`

Handles property data retrieval, token management, and data enhancement from Precisely.

| Method | Description |
|--------|-------------|
| `get_new_token()` | Retrieves a new authentication token. |
| `refresh_token()` | Refreshes the token after expiration. |
| `fetch_data(query)` | Executes a GraphQL query and fetches results from the Precisely API. |
| `get_data(query, *path)` | Retrieves specific data using a query and nested path from the response. |

## Output Data Format

The final output is saved as a CSV file. The table below describes some of the key columns in the output file:

| Column Name | Description |
|-------------|-------------|
| `precisely_id` | Unique identifier for the property or location in Precisely's dataset. |
| `psyte_PSYTECategoryCode` | PSYTE category classification for the location. |
| `coastal_nearestWaterbodyName` | Name of the nearest water body. |
| `flood_floodZoneBaseFloodElevationFeet` | Elevation in feet for the base flood zone. |


## Analysis Results

#### Income and Demographics Analysis:

The data reveals that the largest population segment falls in the Bottom 30-49.99% income bracket, while 85.5% of the population resides in urban areas. Property ownership patterns show that most properties are either mortgaged or rented, with single-family residences being the predominant housing type. Notably, over 15,000 households are in the Bottom 10-19.99% value bracket, and about 3,000 households are classified as 'Rustic Blue Collar', with only a small fraction falling under the 'Stylish Suburbs' category.

![Average Sale Amount by Property Type](presentations&docs/avgSaleamtbyPT.png)
![Coastal Distance by Urban Rural Class](presentations&docs/coastalDistancebyUrbanRuralClass.png)
![Customer Count Distribution](presentations&docs/customer_count.png)

#### Location and Property Value Analysis:

The study demonstrates that location factors such as elevation and coastal distance have a stronger correlation with sale prices compared to property characteristics like the number of bedrooms or bathrooms. While single-family homes dominate the market across all size ranges, there's significant price variation within similar square footage, indicating that factors like location, condition, and amenities play crucial roles in determining property values. Urban development shows a clear preference for coastal proximity, with rural properties typically located further inland. Additionally, some of the highest-value properties are found at moderate elevations, suggesting a balance between coastal proximity and flood risk protection. The analysis also reveals that while income correlates with property value, there's significant overlap across income brackets, and even among top ZIP codes, there's considerable variation in both total property count and property type mix, with single-family homes consistently being the dominant type.

### Property Analysis
![Elevation vs Sales Analysis](presentations&docs/elevationvSSales.png)
![Living Space Distribution](presentations&docs/living_space.png)
![Market Distribution](presentations&docs/market_distribution.png)

#### Potential Customer Cluster Analysis:

Moderate Economic Homes primarily dominate the market at 52.1%, while Luxury Estates and Mixed Properties comprise 40% of the market share. Premium Urban Homes (20,000 units) and Luxury Estates (9,300 units) are the leading segments, suggesting a clear opportunity for a two-tiered service strategy. Property values show an inverse relationship with customer volume, indicating the need for differentiated service approaches 

- premium services for Luxury Estates (average value $2.8M) and volume-based packages for premium urban homes. Mixed Properties and Luxury Estates, with their large living spaces (23,434 and 18,313 sq ft, respectively), require comprehensive coverage solutions.

![Metrics Heatmap](presentations&docs/metrics_heatmap.png)
![Multi-metric Analysis](presentations&docs/multi_metric.png)


#### Unserved Market and Service Strategy Analysis:

A significant unserved market of 17,223 properties exists, with single-family homes comprising 41.5% of this segment, predominantly in lower-income brackets. Urban Mixed Housing represents 60.2% of the unserved market, followed by Large Unclassified Properties at 22.5%. The contrast between Luxury Urban Apartments ($4.2M value, 24,496 sq ft) and Large Unclassified Properties ($231K value, 17,253 sq ft) necessitates distinct service strategies. Property characteristics vary significantly across segments - from Urban Value homes (10,372 properties, $315K average value) requiring basic internet with competitive pricing, to rural properties (2,014 units) needing specialized solutions like satellite/wireless options and weather-resistant packages. The variation in bedroom count across segments (ranging from 4.1 in Unclassified Properties to 2.5 in Luxury Apartments) further emphasizes the need for customized coverage solutions based on household size and composition.

![Property Value Analysis](presentations&docs/propertyValueanalysis.png)
![Property Type Distribution](presentations&docs/propertytypedistribution.png)
![Unserved Market Analysis](presentations&docs/unserved_market.png)

## Security Note

This is a Capstone Project, under the guidance of Lebow School of Business.

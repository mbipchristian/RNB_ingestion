# RNB API Data Description

This notebook uses building data from the RNB (Référentiel National des Bâtiments) API, which provides detailed information about buildings in France. The data includes:
* Unique building identifiers (`rnb_id`)
* Geographical coordinates and shapes
* Address details
* External IDs from various sources
* Status and activity flags
* Associated plot information

The API endpoint used in this notebook is:
```
https://rnb-api.beta.gouv.fr/api/alpha/buildings/?insee_code=33063&limit=100
```
This endpoint returns buildings for the INSEE code `33063` (Bordeaux), with a limit of 100 records per request. The API supports pagination for retrieving additional records.

# How to Run This Notebook and Prerequisites

This notebook ingests building data from the RNB API and stores it in MongoDB. To run it successfully, follow these steps:

## Prerequisites
* **Python 3.8+** installed on your machine
* **MongoDB Atlas** account and cluster (update the connection string with your credentials)
* **Databricks** account 

* **Required Python packages:**
  * `pyspark`
  * `requests`
  * `pydantic`
  * `pymongo`

Install packages using pip:
```bash
pip install pyspark requests pydantic pymongo
```

## How to Run
1. **Update MongoDB credentials** in the connection string in Cell 6.
2. **Open the notebook in Databricks.**
3. **Run each cell sequentially** from top to bottom.
4. The script will fetch building data from the RNB API and store it in your MongoDB collection.

## Notes
* Ensure your MongoDB cluster allows connections from your IP address.
* The API endpoint and parameters can be modified as needed.
* For large data volumes, consider batching or pagination strategies.

If you encounter issues, check package installation, network connectivity, and MongoDB access permissions.

## Autors
Developped by Christian MBIPBIPE FOUEMKEU and Omar BAYOUD
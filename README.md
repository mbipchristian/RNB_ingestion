%md
# How to Run This Notebook and Prerequisites

This notebook ingests building data from the RNB API and stores it in MongoDB. To run it successfully, follow these steps:

## Prerequisites
* **Python 3.8+** installed on your machine
* **MongoDB Atlas** account and cluster (update the connection string with your credentials)
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
2. **Open the notebook in Databricks or Jupyter.**
3. **Run each cell sequentially** from top to bottom.
4. The script will fetch building data from the RNB API and store it in your MongoDB collection.

## Notes
* Ensure your MongoDB cluster allows connections from your IP address.
* The API endpoint and parameters can be modified as needed.
* For large data volumes, consider batching or pagination strategies.

If you encounter issues, check package installation, network connectivity, and MongoDB access permissions.
Data Management
===============

Check InfluxDB Credentials
--------------------------

Ensure you have the following InfluxDB credentials:

.. code-block:: python

   INFLUXDB_URL = "..."  # InfluxDB URL
   TOKEN = "..."         # Your token
   ORG = "..."           # Your organization name
   BUCKET = "..."        # Your bucket name

Upload a Dataset from InfluxDB to ClearML Server Storage
--------------------------------------------------------

Use the ClearML Data Management tool to upload datasets. The following Python code serves as a template:

.. code-block:: python

   from clearml import Dataset
   from influxdb import InfluxDBClient
   import pandas as pd
   import time
   import io

   # InfluxDB credentials and configurations
   INFLUXDB_URL = "..."  # Replace with your InfluxDB URL
   TOKEN = "..."         # Your token
   ORG = "..."           # Your organization name
   BUCKET = "..."        # Your bucket name

   # Create InfluxDB client
   client = InfluxDBClient(url=INFLUXDB_URL, token=TOKEN)

   # Define the number of days you want to retrieve data from
   X_days = 30  # Change this to the number of days you need

   # Calculate the timestamp X days ago from the current time
   end_time = int(time.time() * 1e9)  # Current time in nanoseconds
   start_time = end_time - (X_days * 24 * 3600 * 1e9)  # X days ago in nanoseconds

   # Query data from InfluxDB
   query = f'SELECT * FROM "randata" WHERE time >= {start_time} AND time <= {end_time}'
   result = client.query(query)

   # Convert the result to a Pandas DataFrame
   df = pd.DataFrame(result.get_points())

   # Convert the DataFrame to a CSV-like string in memory
   csv_buffer = io.StringIO()
   df.to_csv(csv_buffer, index=False)
   csv_buffer.seek(0)  # Reset the buffer to the beginning

   # Upload the in-memory CSV data to ClearML
   dataset = Dataset.create(dataset_name="rapp_data", dataset_project="rapp_examples")
   dataset.add_object(csv_buffer, "randata.csv")  # Add the in-memory CSV as an object with a filename
   dataset.upload()
   dataset.finalize()

   print(f"Dataset uploaded to ClearML with ID: {dataset.id}")

Accessing the Uploaded Data and Preparing It
--------------------------------------------

.. code-block:: python

   from clearml import Dataset
   import os
   import pandas as pd
   from pathlib import Path
   from clearml import Task

   # Initialize ClearML Task
   task = Task.init(project_name="Your-Project", task_name="Data Preparation", output_uri=True)

   # Load dataset from ClearML
   dataset_name = "your-dataset-name"
   dataset_project = "your-dataset-project"
   local_dataset_path = Path(Dataset.get(
       dataset_project=dataset_project,
       dataset_name=dataset_name,
       alias="dataset-alias"
   ).get_local_copy())

   # List all files in the dataset directory
   data_files = [data_path for data_path in os.listdir(local_dataset_path) if data_path.endswith(".csv")]
   print("Data files:", data_files)

   # Function to preprocess a single CSV file
   def process_file(file_path):
       df = pd.read_csv(file_path)
       # ... code for preparing the data ...
       return df

   # Process all data files
   dataframes = [process_file(os.path.join(local_dataset_path, file)) for file in data_files]

   # Combine into a single DataFrame
   combined_data = pd.concat(dataframes, axis=0).reset_index(drop=True)

   # Display basic statistics
   print(combined_data.describe())

Preprocessing and Normalization
-------------------------------

.. code-block:: python

   import pandas as pd
   from sklearn.preprocessing import StandardScaler, MinMaxScaler

   def preprocess_data(df, numeric_cols=None, fill_strategy="mean", drop_cols=None):
       """
       Preprocess the dataset:
       - Fill missing values
       - Drop unnecessary columns
       - Ensure consistent data types
       """
       # Drop unnecessary columns if specified
       if drop_cols:
           df = df.drop(columns=drop_cols, errors="ignore")

       # Fill missing values
       if fill_strategy == "mean":
           df = df.fillna(df.mean(numeric_only=True))
       elif fill_strategy == "median":
           df = df.fillna(df.median(numeric_only=True))
       elif fill_strategy == "zero":
           df = df.fillna(0)
       elif fill_strategy == "ffill":
           df = df.fillna(method="ffill")
       elif fill_strategy == "bfill":
           df = df.fillna(method="bfill")
       else:
           raise ValueError("Unsupported fill strategy. Use 'mean', 'median', 'zero', 'ffill', or 'bfill'.")

       # Ensure numeric columns are of proper type
       if numeric_cols:
           df[numeric_cols] = df[numeric_cols].apply(pd.to_numeric, errors="coerce")

       return df

   def normalize_data(df, cols_to_normalize, method="standard"):
       """
       Normalize the specified columns using the given method:
       - 'standard': StandardScaler (z-score normalization)
       - 'minmax': MinMaxScaler (scale to [0, 1])
       """
       if method == "standard":
           scaler = StandardScaler()
       elif method == "minmax":
           scaler = MinMaxScaler()
       else:
           raise ValueError("Unsupported normalization method. Use 'standard' or 'minmax'.")

       df[cols_to_normalize] = scaler.fit_transform(df[cols_to_normalize])

       return df

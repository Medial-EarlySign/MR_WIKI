# Data Fetcher
How to write data fetcher.
 
Here is and example of simple data fetcher when you have multiple files in specific folder that you want to read one by one. There is also example for reading for DB.
data_fetcher is function that receives : batch_size, start_batch and we can iterate through it and get each time the next dataframe.
 
**Our data fetcher for IBM **
```python
import pandas as pd
import os, gzip, sys
from ETL_Infra.data_fetcher.files_fetcher import files_fetcher, list_directory_files
#End of imports - the data_fetchet function that will be used
 
def ibm_parser_diagnosis(batch_size, start_batch):
    files = list_ibm_files('v_diagnosis')
    columns=['pid', 'source_system_type', 'source_connection_type', 'rowid', 'update_date', 'diagnosis_code', 'icd_code', 'icd_version',\
 'encounter_join_id', 'encounter_join_hash', 'diagnosis_date', 
            'is_principal', 'is_admitting', 'is_reason_for_visit', 'status', 'claim_type', 'snomed_join_id']
    return files_fetcher(files, batch_size, lambda f: ibm_file_parser(f, columns)  , start_batch)
#The parsing function - in this case- very simple pd.read_csv with several arguments (like columns names that is not in the files) and optional argument to read only specific columns
 
def ibm_file_parser(f, columns):
    return pd.read_csv(f, compression='gzip', sep='|', header=None, index_col=False, names=columns)
 
#Helper function to list files in the folder (IBM data dir) with specific prefix
def list_ibm_files(file_prefix):
    base_path='/mnt/s3mnt_pt'
    return list_directory_files(base_path, file_prefix)
```

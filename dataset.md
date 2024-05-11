# Store Public Google Sheet Data

**Description:**
This DAG (Directed Acyclic Graph) automates the process of downloading data from a public Google Sheets spreadsheet and storing it locally as a CSV file. The DAG is designed to run on a daily basis, ensuring that the dataset is regularly updated with the latest information from the spreadsheet.

**Tasks:**
1. **download_and_store_data:**
   - This task executes a Python function (`download_and_store_data()`) that accesses the specified public Google Sheets spreadsheet, retrieves all its data, and saves it as a CSV file in the designated path (`/home/admin/dags/dataset/data.csv`).

**Schedule:**
The DAG is scheduled to run daily (`schedule_interval='@daily'`), starting from May 10, 2024. This ensures that the data is refreshed regularly, keeping the local dataset up to date with any changes made to the Google Sheets spreadsheet.

**Owner:** airflow (Owner of the DAG)

**Dependencies:**
- The DAG does not depend on past runs (`depends_on_past=False`).
- Email notifications on failure or retry are disabled (`email_on_failure=False`, `email_on_retry=False`).
- The DAG allows for 1 retry (`retries=1`), in case of any failures during execution.

---

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime
import gspread

def download_and_store_data():
    # Replace '<public_spreadsheet_url>' with the actual URL of the Google Sheets spreadsheet
    spreadsheet_url = '<public_spreadsheet_url>'
    client = gspread.open_by_url(spreadsheet_url)

    # Access a specific worksheet by its index or title
    worksheet = client.sheet1  # Change 'sheet1' to the appropriate title or index

    # Get all values from the worksheet
    data = worksheet.get_all_values()

    # Specify the path to store data
    save_path = '/home/admin/dags/dataset/data.csv'

    # Store the data
    with open(save_path, 'w') as file:
        for row in data:
            file.write(','.join(row) + '\n')

# Define the default arguments
default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2024, 5, 10),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
}

# Define the DAG
dag = DAG(
    'store_public_google_sheet_data',
    default_args=default_args,
    description='''This DAG automates the process of downloading data from a public Google Sheets spreadsheet and storing it locally as a CSV file. The DAG is designed to run on a daily basis, ensuring that the dataset is regularly updated with the latest information from the spreadsheet.''',
    schedule_interval='@daily',
)

# Define the task to download and store data
download_and_store_task = PythonOperator(
    task_id='download_and_store_data',
    python_callable=download_and_store_data,
    dag=dag,
)

# Set task dependencies
download_and_store_task
```

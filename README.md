# Airflow DAG Tutorial and Schaduler

## Airflow DAG Tutorial

### Introduction
Apache Airflow is an open-source platform used to programmatically author, schedule, and monitor workflows. Directed Acyclic Graphs (DAGs) in Airflow are the core component that represents a workflow.

### 1. Simple DAG to Print Hello World
**Description**: This DAG is a straightforward example demonstrating the basic structure of an Airflow DAG. It consists of a single task called `print_hello`, which executes a Python function to print "Hello World!". The DAG is scheduled to run daily starting from January 1, 2024. It's a simple yet essential example to understand the DAG's anatomy and scheduling capabilities.
```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime

def print_hello():
    return 'Hello world!'

dag = DAG('hello_world', description='Simple DAG to print Hello World',
          schedule_interval='@daily',
          start_date=datetime(2024, 1, 1),
          catchup=False)

task_hello = PythonOperator(
    task_id='print_hello',
    python_callable=print_hello,
    dag=dag
)
```

### 2. DAG with Multiple Tasks
**Description**: In this DAG, we introduce multiple tasks connected sequentially. It starts with a dummy task called `start`, followed by a Python task named `print_hello`, which prints "Hello World!". Finally, it ends with another dummy task named `end`. This DAG runs hourly starting from January 1, 2024. It demonstrates how to define dependencies between tasks in a workflow.

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.python_operator import PythonOperator
from datetime import datetime

def print_hello():
    return 'Hello world!'

dag = DAG('multiple_tasks', description='DAG with multiple tasks',
          schedule_interval='@hourly',
          start_date=datetime(2024, 1, 1),
          catchup=False)

task_start = DummyOperator(task_id='start', dag=dag)
task_hello = PythonOperator(task_id='print_hello', python_callable=print_hello, dag=dag)
task_end = DummyOperator(task_id='end', dag=dag)

task_start >> task_hello >> task_end
```

### 3. Conditional Execution DAG

**Description**: This DAG showcases conditional execution based on a branching condition. The `decide_branch` task decides whether to execute the `morning_task` or `afternoon_task` based on the current time. If the current time is before 12 PM, it executes the morning task; otherwise, it executes the afternoon task. This demonstrates Airflow's ability to handle branching logic within workflows.

```python
from airflow import DAG
from airflow.operators.python_operator import BranchPythonOperator, PythonOperator
from datetime import datetime

def decide_branch():
    if datetime.now().hour < 12:
        return 'morning_task'
    else:
        return 'afternoon_task'

def morning_task():
    return 'Good morning!'

def afternoon_task():
    return 'Good afternoon!'

dag = DAG('conditional_execution', description='Conditional execution DAG',
          schedule_interval='@daily',
          start_date=datetime(2024, 1, 1),
          catchup=False)

task_decide = BranchPythonOperator(
    task_id='decide_branch',
    python_callable=decide_branch,
    dag=dag
)

task_morning = PythonOperator(
    task_id='morning_task',
    python_callable=morning_task,
    dag=dag
)

task_afternoon = PythonOperator(
    task_id='afternoon_task',
    python_callable=afternoon_task,
    dag=dag
)

task_decide >> [task_morning, task_afternoon]
```
**Description**: This DAG decides whether to execute the morning_task or afternoon_task based on the current time. It runs daily starting from January 1, 2024.

### 4. Parameterized DAG
**Description**: Here, we introduce a parameterized DAG where the task `print_message` accepts a parameter (`message`) and prints it. This enables dynamic behavior within tasks, allowing you to pass parameters and customize task execution based on external factors. The DAG runs daily starting from January 1, 2024, showcasing how to incorporate dynamic data into your workflows.

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime

def print_message(message):
    return message

dag = DAG('parameterized_dag', description='Parameterized DAG',
          schedule_interval='@daily',
          start_date=datetime(2024, 1, 1),
          catchup=False)

task_print = PythonOperator(
    task_id='print_message',
    python_callable=print_message,
    op_kwargs={'message': 'This is a parameterized message!'},
    dag=dag
)
```
### 5. Error Handling DAG
**Description**: This DAG focuses on error handling within workflows. The `error_task` intentionally raises an exception to simulate an error. The `error_callback` task serves as an error handler and is triggered only if the `error_task` fails. It demonstrates how to implement error handling strategies within Airflow DAGs, ensuring robustness and reliability in your workflow executions.

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime

def error_function():
    # Simulate an error
    raise Exception('An error occurred!')

def error_callback():
    return 'Error handled successfully'

dag = DAG('error_handling', description='Error handling DAG',
          schedule_interval='@daily',
          start_date=datetime(2024, 1, 1),
          catchup=False)

task_error = PythonOperator(
    task_id='error_task',
    python_callable=error_function,
    dag=dag
)

task_error_handler = PythonOperator(
    task_id='error_callback',
    python_callable=error_callback,
    trigger_rule='one_failed',
    dag=dag
)

task_error >> task_error_handler
```
**Description**: This DAG intentionally raises an error in one task and then handles it in another task. It runs daily starting from January 1, 2024.

### Conclusion
These examples provide a basic understanding of how to create Airflow DAGs using dummy data. With Airflow's flexibility, you can build complex workflows to automate and manage your data pipelines effectively.



# Airflow Sample DAGs

This repository contains sample DAGs for Apache Airflow, demonstrating different scheduling intervals and error handling.

## DAGs

1. **Hourly DAG**: Executes a task every hour.
2. **Daily DAG**: Executes a task once every day.
3. **Monthly DAG**: Executes a task once every month.
4. **Specific Hour in a Day DAG**: Executes a task at a specific hour of the day.
5. **Specific Day of Month DAG**: Executes a task on a specific day of the month.
6. **Error Handling DAG**: Executes an error handling task when any task in the DAG fails.

## Hourly DAG (`hourly_dag.py`)

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2024, 5, 6),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

with DAG(
    'hourly_dag',
    default_args=default_args,
    description='Execute a task every hour',
    schedule_interval='@hourly',
) as dag:

    start_task = DummyOperator(task_id='start_task')
    hourly_task = DummyOperator(task_id='hourly_task')
    end_task = DummyOperator(task_id='end_task')

    start_task >> hourly_task >> end_task
```

## Daily DAG (`daily_dag.py`)

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2024, 5, 6),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

with DAG(
    'daily_dag',
    default_args=default_args,
    description='Execute a task once every day',
    schedule_interval='@daily',
) as dag:

    start_task = DummyOperator(task_id='start_task')
    daily_task = DummyOperator(task_id='daily_task')
    end_task = DummyOperator(task_id='end_task')

    start_task >> daily_task >> end_task
```

## Specific Hour in a Day DAG (`specific_hour_dag.py`)

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2024, 5, 6),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

with DAG(
    'specific_hour_dag',
    default_args=default_args,
    description='Execute a task at a specific hour of the day',
    schedule_interval='0 12 * * *',  # Execute at 12:00 PM UTC every day
) as dag:

    start_task = DummyOperator(task_id='start_task')
    specific_hour_task = DummyOperator(task_id='specific_hour_task')
    end_task = DummyOperator(task_id='end_task')

    start_task >> specific_hour_task >> end_task
```

## Specific Day of Month DAG (`specific_day_month_dag.py`)

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2024, 5, 6),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

with DAG(
    'specific_day_month_dag',
    default_args=default_args,
    description='Execute a task on a specific day of the month',
    schedule_interval='0 0 15 * *',  # Execute at midnight (UTC) on the 15th day of every month
) as dag:

    start_task = DummyOperator(task_id='start_task')
    specific_day_task = DummyOperator(task_id='specific_day_task')
    end_task = DummyOperator(task_id='end_task')

    start_task >> specific_day_task >> end_task
```

## Error Handling DAG (`error_handling_dag.py`)

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.python_operator import PythonOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2024, 5, 6),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

def error_handler_task():
    print("This task handles errors")

with DAG(
    'error_handling_dag',
    default_args=default_args,
    description='Execute an error handling task when any task in the DAG fails',
    schedule_interval=None,
) as dag:

    start_task = DummyOperator(task_id='start_task')
    error_handler_task = PythonOperator(
        task_id='error_handler_task',
        python_callable=error_handler_task,
    )
    end_task = DummyOperator(task_id='end_task')

    start_task >> error_handler_task >> end_task
```
## Conclusion

The scheduler DAGs provided in this repository offer a comprehensive overview of different scheduling intervals and error handling strategies within Apache Airflow. By exploring these examples, users can gain insights into how to effectively schedule and manage workflows, ensuring reliable and efficient execution of tasks. Additionally, the error handling DAG demonstrates the importance of implementing robust error handling mechanisms to maintain the integrity of data pipelines.

With the flexibility and power of Apache Airflow, users can tailor these examples to suit their specific use cases, enabling seamless automation and orchestration of complex workflows. Whether it's executing tasks at specific time intervals, handling errors gracefully, or customizing task dependencies, Airflow provides a robust framework for building scalable and resilient data pipelines.

Explore these scheduler DAGs, experiment with different configurations, and leverage Airflow's capabilities to streamline your data engineering workflows.


# Tutorial: Checking PostgreSQL Connection Using a Saved Connection

**Code Example:**
```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from airflow.utils.dates import days_ago
from airflow.hooks.postgres_hook import PostgresHook

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': days_ago(1),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
}

dag = DAG(
    'postgres_connection_check_saved_connection',
    default_args=default_args,
    description='A DAG to check PostgreSQL connection using a saved connection',
    schedule_interval=None,
    tags=['postgres', 'connection', 'check']
)

def check_postgres_connection():
    try:
        # Initialize PostgresHook with the saved connection ID
        postgres_hook = PostgresHook(postgres_conn_id='akad')
        
        # Get a connection to the PostgreSQL database
        conn = postgres_hook.get_conn()
        
        # Execute a simple query to check the connection
        cursor = conn.cursor()
        cursor.execute("SELECT 1")
        result = cursor.fetchone()
        
        print("Connection to PostgreSQL successful. Result:", result)
        
        # Close connections
        cursor.close()
        conn.close()
        
    except Exception as e:
        print("Error connecting to PostgreSQL:", e)

check_postgres_connection_task = PythonOperator(
    task_id='check_postgres_connection',
    python_callable=check_postgres_connection,
    dag=dag,
)

check_postgres_connection_task
```

### Tutorial: Checking PostgreSQL Connection Using Manual Connection Details

**Code Example:**
```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from airflow.utils.dates import days_ago
import psycopg2

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': days_ago(1),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
}

dag = DAG(
    'postgres_connection_check_manual_connection',
    default_args=default_args,
    description='A DAG to check PostgreSQL connection using manual connection details',
    schedule_interval=None,
    tags=['postgres', 'connection', 'check']
)

def check_postgres_connection():
    try:
        # Establish connection
        conn = psycopg2.connect(
            host="your_host",
            port="your_port",
            database="your_database",
            user="your_username",
            password="your_password"
        )
        
        # Execute a simple query to check the connection
        cursor = conn.cursor()
        cursor.execute("SELECT 1")
        result = cursor.fetchone()
        
        print("Connection to PostgreSQL successful. Result:", result)
        
        # Close connections
        cursor.close()
        conn.close()
        
    except Exception as e:
        print("Error connecting to PostgreSQL:", e)

check_postgres_connection_task = PythonOperator(
    task_id='check_postgres_connection',
    python_callable=check_postgres_connection,
    dag=dag,
)

check_postgres_connection_task
```

These code examples provide the necessary setup and task definition to check the PostgreSQL connection using both a saved connection and manual connection details in Apache Airflow.

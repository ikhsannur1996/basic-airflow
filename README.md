## Airflow DAG Tutorial

### Introduction
Apache Airflow is an open-source platform used to programmatically author, schedule, and monitor workflows. Directed Acyclic Graphs (DAGs) in Airflow are the core component that represents a workflow.

### 1. Simple DAG to Print Hello World
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
**Description**: This DAG consists of a single task that prints "Hello World!" daily starting from January 1, 2024.

### 2. DAG with Multiple Tasks
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
**Description**: This DAG consists of three tasks: start, print_hello, and end. It runs hourly starting from January 1, 2024. The print_hello task prints "Hello World!".

### 3. Conditional Execution DAG
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
**Description**: This DAG prints a parameterized message. It runs daily starting from January 1, 2024.

### 5. Error Handling DAG
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

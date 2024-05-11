# DAG Operator

### DAG 1: BashOperator DAG

```python
from airflow import DAG
from airflow.operators.bash_operator import BashOperator
from datetime import datetime

# Define the DAG
dag_bash = DAG(
    'bash_operator_dag',
    description='DAG with BashOperator',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['bash', 'operator']
)

# Define tasks
start_task_bash = BashOperator(
    task_id='start',
    bash_command='echo "Start"',
    dag=dag_bash,
)

end_task_bash = BashOperator(
    task_id='end',
    bash_command='echo "End"',
    dag=dag_bash,
)

# Define task dependencies
start_task_bash >> end_task_bash
```

### DAG 2: PythonOperator DAG

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime

# Define the DAG
dag_python = DAG(
    'python_operator_dag',
    description='DAG with PythonOperator',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['python', 'operator']
)

# Define tasks
def python_function():
    print("Hello from PythonOperator")

python_task_start = PythonOperator(
    task_id='start',
    python_callable=python_function,
    dag=dag_python,
)

python_task_end = PythonOperator(
    task_id='end',
    python_callable=python_function,
    dag=dag_python,
)

# Define task dependencies
python_task_start >> python_task_end
```

### DAG 3: EmailOperator DAG

```python
from airflow import DAG
from airflow.operators.email_operator import EmailOperator
from datetime import datetime

# Define the DAG
dag_email = DAG(
    'email_operator_dag',
    description='DAG with EmailOperator',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['email', 'operator']
)

# Define tasks
email_task_start = EmailOperator(
    task_id='start',
    to='example@example.com',
    subject='Airflow Email Test',
    html_content='<p>Start</p>',
    dag=dag_email,
)

email_task_end = EmailOperator(
    task_id='end',
    to='example@example.com',
    subject='Airflow Email Test',
    html_content='<p>End</p>',
    dag=dag_email,
)

# Define task dependencies
email_task_start >> email_task_end
```

### DAG 4: SlackAPIPostOperator DAG

```python
from airflow import DAG
from airflow.operators.slack_operator import SlackAPIPostOperator
from datetime import datetime

# Define the DAG
dag_slack = DAG(
    'slack_operator_dag',
    description='DAG with SlackAPIPostOperator',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['slack', 'operator']
)

# Define tasks
slack_task_start = SlackAPIPostOperator(
    task_id='start',
    token='your_slack_token',
    channel='#channel_name',
    text='Start',
    dag=dag_slack,
)

slack_task_end = SlackAPIPostOperator(
    task_id='end',
    token='your_slack_token',
    channel='#channel_name',
    text='End',
    dag=dag_slack,
)

# Define task dependencies
slack_task_start >> slack_task_end
```

### DAG 5: PostgresOperator DAG

```python
from airflow import DAG
from airflow.operators.postgres_operator import PostgresOperator
from datetime import datetime

# Define the DAG
dag_postgres = DAG(
    'postgres_operator_dag',
    description='DAG with PostgresOperator',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['postgres', 'operator']
)

# Define tasks
postgres_task_start = PostgresOperator(
    task_id='start',
    postgres_conn_id='your_postgres_conn_id',
    sql='SELECT * FROM your_table WHERE id = 1;',
    dag=dag_postgres,
)

postgres_task_end = PostgresOperator(
    task_id='end',
    postgres_conn_id='your_postgres_conn_id',
    sql='SELECT * FROM your_table WHERE id = 2;',
    dag=dag_postgres,
)

# Define task dependencies
postgres_task_start >> postgres_task_end
```

These DAGs include two tasks each for the respective operators, along with their dependencies. Remember to replace placeholder values like `'your_slack_token'` and `'your_postgres_conn_id'` with your actual credentials and configurations.

# DAG Dependency Examples

### Linear Dependency

#### Description:
Tasks are executed sequentially, one after the other.

#### DAG Definition:

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime

dag = DAG(
    'linear_dependency',
    description='DAG with linear dependency',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['linear', 'dependency']
)

task_1 = DummyOperator(task_id='task_1', dag=dag)
task_2 = DummyOperator(task_id='task_2', dag=dag)
task_3 = DummyOperator(task_id='task_3', dag=dag)

task_1 >> task_2 >> task_3
```

### Fan-out/Fan-in Dependency

#### Description:
One task triggers multiple tasks to run in parallel, and then another task depends on all of them.

#### DAG Definition:

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime

dag = DAG(
    'fan_out_fan_in',
    description='DAG with fan-out/fan-in dependency',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['fan-out', 'fan-in']
)

task_start = DummyOperator(task_id='start', dag=dag)
task_1 = DummyOperator(task_id='task_1', dag=dag)
task_2 = DummyOperator(task_id='task_2', dag=dag)
task_3 = DummyOperator(task_id='task_3', dag=dag)
task_end = DummyOperator(task_id='end', dag=dag)

task_start >> [task_1, task_2, task_3] >> task_end
```

### Branching Dependency

#### Description:
A task decides which tasks to execute based on some condition.

#### DAG Definition:

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.python_operator import BranchPythonOperator
from datetime import datetime

dag = DAG(
    'branching_dependency',
    description='DAG with branching dependency',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['branching', 'dependency']
)

def decide_branch(**kwargs):
    if condition:
        return 'task_a'
    else:
        return 'task_b'

branch_task = BranchPythonOperator(
    task_id='branch_task',
    python_callable=decide_branch,
    provide_context=True,
    dag=dag,
)

task_a = DummyOperator(task_id='task_a', dag=dag)
task_b = DummyOperator(task_id='task_b', dag=dag)
task_end = DummyOperator(task_id='end', dag=dag)

branch_task >> [task_a, task_b] >> task_end
```

### Join Dependency

#### Description:
Tasks are executed in parallel, and then another task depends on all of them.

#### DAG Definition:

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime

dag = DAG(
    'join_dependency',
    description='DAG with join dependency',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['join', 'dependency']
)

task_start = DummyOperator(task_id='start', dag=dag)
task_1 = DummyOperator(task_id='task_1', dag=dag)
task_2 = DummyOperator(task_id='task_2', dag=dag)
task_3 = DummyOperator(task_id='task_3', dag=dag)
task_join = DummyOperator(task_id='join', dag=dag)
task_end = DummyOperator(task_id='end', dag=dag)

[task_1, task_2, task_3] >> task_join >> task_end
```

### Complex Dependency

#### Description:
Combination of linear, branching, and join dependencies.

#### DAG Definition:

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime

dag = DAG(
    'complex_dependency',
    description='DAG with complex dependency',
    schedule_interval='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['complex', 'dependency']
)

task_start = DummyOperator(task_id='start', dag=dag)
task_1 = DummyOperator(task_id='task_1', dag=dag)
task_2 = DummyOperator(task_id='task_2', dag=dag)
task_3 = DummyOperator(task_id='task_3', dag=dag)
task_4 = DummyOperator(task_id='task_4', dag=dag)
task_join = DummyOperator(task_id='join', dag=dag)
task_end = DummyOperator(task_id='end', dag=dag)

task_start >> task_1 >> task_3 >> task_join
task_start >> task_2 >> task_4 >> task_join
task_join >> task_end
```

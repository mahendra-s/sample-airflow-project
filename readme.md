# Apache Airflow Sample Project

Airflow getting started (document)[https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html]
Airflow Docker-Compose File

```commandline
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.1.2/docker-compose.yaml'
```

Airflow setup create folders and process group

```
mkdir ./dags ./logs ./plugins ./config
echo -e "AIRFLOW_UID=$(id -u)\nAIRFLOW_GID=0" > .env
```

Get airflow utility

```
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.1.2/airflow.sh'
chmod +x airflow.sh
```

Initialize database and start services

```
docker-compose up airflow-init
docker-compose up
docker-compose run airflow-worker airflow info
```

Stop and remove all volumes

```
docker-compose down --volumes --rmi all
```

## Some FAQ

### Airflow config

configurations-ref (link)[https://airflow.apache.org/docs/apache-airflow/stable/configurations-ref.html]

### Why is task not getting scheduled?

(
why-isn-t-my-task-getting-scheduled)[https://airflow.apache.org/docs/apache-airflow/stable/faq.html#why-isn-t-my-task-getting-scheduled]

Quick Summary Airflow can be a bit tricky to setup.

Do you have the airflow scheduler running? Do you have the airflow webserver running? Have you checked that all DAGs you
want to run are set to On in the web ui? Do all the DAGs you want to run have a start date which is in the past? Do all
the DAGs you want to run have a proper schedule which is shown in the web ui? If nothing else works, you can use the web
ui to click on the dag, then on Graph View. Now select the first task and click on Task Instance. In the paragraph Task
Instance Details you will see why a DAG is waiting or not running. I've had for instance a DAG which was wrongly set to
depends_on_past: True which forbid the current instance to start correctly.
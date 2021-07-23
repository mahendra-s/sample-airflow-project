# Apache Airflow Sample Project

Airflow getting started [document](https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html). Airflow
Docker-Compose File

```
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

## Mysql DB backend setup

1. Disabled postgres db set via config variable, service and dependency service
2. Setting MySql database on one of the host MySql Database
   setup [link](http://airflow.apache.org/docs/apache-airflow/stable/howto/set-up-database.html#setting-up-a-mysql-database)
3. Create database schema and user as

```
CREATE DATABASE airflow_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'airflow_user' IDENTIFIED BY 'airflow_pass';
GRANT ALL PRIVILEGES ON airflow_db.* TO 'airflow_user';
```

4. Setting database connection string via config as

```
[core]
sql_alchemy_conn = mysql+mysqldb://airflow_user:airflow_pass@192.168.1.9:3306/airflow_db
[celery
result_backend = db+mysql://airflow_user:airflow_pass@192.168.1.9/airflow_db
```

5. Setup DDL schema via

```
docker-compose up airflow-init
or
./airflow.sh db init
```

6. Start airflow

```
 docker-compose up
```

## Some FAQ

### Airflow config

configurations-ref [link](https://airflow.apache.org/docs/apache-airflow/stable/configurations-ref.html)

### Why is task not getting scheduled?

[why-isn-t-my-task-getting-scheduled](https://airflow.apache.org/docs/apache-airflow/stable/faq.html#why-isn-t-my-task-getting-scheduled)

Quick Summary Airflow can be a bit tricky to setup.

Do you have the airflow scheduler running? Do you have the airflow webserver running? Have you checked that all DAGs you
want to run are set to On in the web ui? Do all the DAGs you want to run have a start date which is in the past? Do all
the DAGs you want to run have a proper schedule which is shown in the web ui? If nothing else works, you can use the web
ui to click on the dag, then on Graph View. Now select the first task and click on Task Instance. In the paragraph Task
Instance Details you will see why a DAG is waiting or not running. I've had for instance a DAG which was wrongly set to
depends_on_past: True which forbid the current instance to start correctly.
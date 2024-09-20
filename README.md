# Airflow-EC2
This is guide on apache airflow installation on AWS  EC2
# Small EC2 Instance Airflow Tutorial

## EC2 Configuration
- **Instance Type**: T2.small
- **Key Pair**: Create a new key pair
- **Image**: Ubuntu

## Connect to EC2 Instance
After launching your EC2 instance, connect to it in aws


## Commands to Run


   sudo apt update
Install Python 3 and pip:

sudo apt install python3-pip
Install SQLite3:

sudo apt install sqlite3
Install the Python 3.10 virtual environment package:

sudo apt install python3.10-venv
Create a Python virtual environment:

python3 -m venv venv
Activate the virtual environment:

source venv/bin/activate
(Optional) Install PostgreSQL development libraries:

sudo apt-get install libpq-dev
Install Apache Airflow with PostgreSQL support:

pip install "apache-airflow[postgres]==2.5.0" --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.5.0/constraints-3.7.txt"
Initialize the Airflow database:

airflow db init
Install PostgreSQL and its contrib package:

sudo apt-get install postgresql postgresql-contrib
Switch to the PostgreSQL user:

sudo -i -u postgres
Access the PostgreSQL shell:

psql
Create the Airflow database and user:

CREATE DATABASE airflow;
CREATE USER airflow WITH PASSWORD 'airflow';
GRANT ALL PRIVILEGES ON DATABASE airflow TO airflow;
Exit the PostgreSQL shell by pressing Ctrl + D.

Navigate to the Airflow directory:

cd airflow
Replace the connection string in the airflow.cfg file to use PostgreSQL instead of SQLite:

sed -i 's#sqlite:////home/ubuntu/airflow/airflow.db#postgresql+psycopg2://airflow:airflow@localhost/airflow#g' airflow.cfg
Verify the SQL Alchemy connection string:

grep sql_alchemy airflow.cfg
Check the executor configuration:

grep executor airflow.cfg
Replace SequentialExecutor with LocalExecutor:

sed -i 's#SequentialExecutor#LocalExecutor#g' airflow.cfg
Re-initialize the Airflow database:

airflow db init
Create an Airflow user:

airflow users create -u airflow -f airflow -l airflow -r Admin -e airflow@gmail.com
Enter Password: airflow
Repeat Password: airflow
Update security group inbound rules in your EC2 instance:

Type: Custom TCP
Port Range: 8080
Source: Anywhere (IPV4)
Save the rules.
Start the Airflow webserver:

airflow webserver &
Start the Airflow scheduler:

airflow scheduler
Copy the public IPv4 DNS of your EC2 instance and open it in your browser with port 8080:

http://your-ec2-public-ip:8080
Use below dag code as a test. make sure to mkdir dag folder and then put this .py script in there!

from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime

default_args = {
    'owner': 'airflow',
    'start_date': datetime(2023, 1, 1),
    'retries': 1,
}

dag = DAG(
    'my_new_dag',
    default_args=default_args,
    schedule_interval='@daily',
)

start = DummyOperator(task_id='start', dag=dag)
end = DummyOperator(task_id='end', dag=dag)

start >> end

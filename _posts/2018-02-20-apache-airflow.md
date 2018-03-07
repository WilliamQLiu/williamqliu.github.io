---
layout: post
title: Apache Airflow
---


# {{ page.title }}

Apache Airflow is an open source job scheduler made for data pipelines.

## Setup

While the installation is pretty straightforward, getting it to work is a little more detailed:

export AIRFLOW_HOME=~/airflow
pip install apache-airflow
airflow initdb
airflow webserver -p 8080

pip install apache-airflow[devel]
pip install apache-airflow[async]
pip install apache-airflow[crypto]
pip install apache-airflow[mysql]
pip install apache-airflow[rabbitmq]

I had issues with this rabbitmq so I built rabbitmq: 
git clone git://github.com/celery/librabbitmq.git
cd rabbitmq
make install


Setup Gunicorn:
pip install gunicorn==19.3.0  # needed for python 3
Then in my bashrc add:
export PATH=$PATH:~/.local/bin

Generating an RSA public/private-key pair
openssl genrsa -out private.pem 2048

Generating a self-signed certificate
openssl req -new -x509 -key private.pem -out cacert.pem -days 1095

Then you can access the web gui with https:localhost:8080

# In your airflow.cfg under [webserver]
web_server_ssl_cert = path/to/cacert.pem
web_server_ssl_key = path/to/private.pem


## Run

airflow webserver  # shows GUI
airflow scheduler
airflow worker # picks up tasks

## TODO: Distributed Mode

Setup distributed mode using the celery executor



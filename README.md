https://dzlab.github.io/monitoring/2021/12/30/monitoring-stack-docker/

Command
To start the monitoring stack with
$ docker-compose up

To stop the monitoring stack run the following command
$ docker-compose down -v

Grafana
http://localhost:3000
admin
admin

Promethus
http://192.168.10.57:9090

## MONITORING LINUX HOST METRICS WITH THE NODE EXPORTER
tutorial
https://prometheus.io/docs/guides/node-exporter/#installing-and-running-the-node-exporter
https://rm-rf.medium.com/how-to-install-and-configure-prometheus-node-exporter-node-exporter-as-a-service-on-centos-7-50339b086704

## Step 1: We need a user. Create a system user for the installation.
>sudo groupadd prometheus
>sudo useradd prometheus -g prometheus


## Step 2: Log into node_exporter user and download the latest node_exporter package.
>wget --no-check-certificate https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz

## Step 3: Extract & move the node_exporter package and rename the directory to ‘node_exporter’ for your convenience.
>tar xvfz node_exporter-*.*-amd64.tar.gz
>cd node_exporter-*.*-amd64
>./node_exporter

## Step 4: Enable 9100 for your Prometheus server.
#List All Open Ports
>netstat -lntu

#Opening a Port on Linux to Allow TCP Connections
>sudo ufw allow 9100

If you are using CentOS 7 server:
>sudo firewall-cmd --add-port=9100/tcp
>sudo firewall-cmd --reload

## Test
>curl http://localhost:9100/metrics | grep "node_"

Step 5: Create a service file
Create a file /etc/systemd/system/node_exporter.service with following content.
***************************
[Unit]

Description=node_exporter
Wants=network-online.target
After=network-online.target

[Service]

User=prometheus
Group=prometheus
Type=simple
ExecStart=/var/www/node_exporter-1.7.0.linux-amd64/node_exporter

[Install]

WantedBy=multi-user.target
***************************

Since we have created a new unit file, we must reload the systemd daemon, set the service to always run at boot and start it :
>sudo systemctl daemon-reload
>sudo systemctl enable node_exporter.service
>sudo systemctl start node_exporter.service
>sudo systemctl status node_exporter.service



# srtm-TIG
Grafana-based monitoring system for SRTM
## srtm-TIG relies upon three different services: Telegraf, InfluxDB and Grafana
srtm-TIG runs as a docker application through the "docker compose"
  * Grafana: platform for visualizing and monitoring data
  * InfluxDB: time-series database built for storing and analyzing large, time-stamped data values
  * Telegraf: server agent that is used for collecting, processing and writing data. It has a built-in OpcUa interface.

## Setting up srtm-TIG
## Running srtm-TIG
  * In the same directory as srtm-TIG, type: docker compose up -d
  * To see if srtm-TIG has successfully launched, type: docker ps
    - you should see three processes running: grafana, influxDB and telegraf
## Creating dashboards and visualizations
## Stopping srtm-TIG
  * In the same directory as srtm-TIG, type: docker compose down


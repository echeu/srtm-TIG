# srtm-TIG
Grafana-based monitoring system for SRTM
## srtm-TIG relies upon three different services: Telegraf, InfluxDB and Grafana
srtm-TIG runs as a docker application through the "docker compose" command
  * Grafana: platform for visualizing and monitoring data
  * InfluxDB: time-series database built for storing and analyzing large, time-stamped data values
  * Telegraf: server agent that is used for collecting, processing and writing data. It has a built-in OpcUa interface.
## Files
 * .env: holds environment variables used by Telegraf
 * compose.yaml: docker compose configuration file that defines the required services
 * telegraf.conf: configuration file for Telegraf. Defines the input and output protocols and identifies the OpcUA variables available
## Setting up srtm-TIG
Modify the file compose.yaml
  * grafana: 
    - ports: Grafana uses port 3000 by default. If this port is not available, replace the first number by a different port number. We have set this to be 3020.
    - volumes: the first string is a local directory where grafana will store configuration data. By default this is /home/echeu/srtm-TIG/grafana
  * influxdb:
    - ports: InfluxDB uses port 8086 as its default port. In this configuration we have mapped this to 8096.
    - volumes: configuration data is stored in this directory. By default it is set to /home/echeu/srtm-TIG/influxdb
  * telegraf:
    - volumes: location of the telegraf.conf file. By default it is set to /home/echeu/srtm-TIG/telegraf.conf
   
Set up tunneling
  * In some cases you might be running the display web browser on a different machine (i.e. laptop) than docker is running. In this case you will want to tunnel the two ports: 3020 and 8086 to you device.
    - From a command window on your device/laptop, type: **ssh -L 3020:localhost:3020 <username>@<server name>**
      + where the **server name** is the full path to the machine where you are running docker
    - Also type: **ssh -L 8096:localhost:8096 echeu@eepp-bigmem3.physics.arizona.edu**
      + note that we are using the port numbers defined in compose.yaml

Modify the .env file
  * Set the INFLUXDB_TOKEN to be the token created when you started influxDB for the first time.
 
The first time you run srtm-TIG, you will need to initialize a few settings such as the usernames and passwords for Grafana and Influxdb, as well as the security token for InfluxDB.
  * Type the command: **docker compose up -d**
## Running srtm-TIG
  * In the same directory as srtm-TIG, type: **docker compose up -d**
  * To see if srtm-TIG has successfully launched, type: **docker ps**
    - you should see three processes running: grafana, influxDB and telegraf
## Creating dashboards and visualizations
## Stopping srtm-TIG
  * In the same directory as srtm-TIG, type: **docker compose down**


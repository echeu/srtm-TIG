# srtm-TIG
Grafana-based monitoring system for SRTM
## srtm-TIG relies upon three different services: Telegraf, InfluxDB and Grafana
srtm-TIG runs as a docker application through the **docker compose** command
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
  * In some cases you might be running your web browser on a different machine (i.e. laptop) than docker is running. In this case you will want to tunnel the two ports: 3020 and 8086 to your device.
    - From a command window on your device/laptop, type:\
       **ssh -L 3020:localhost:3020 \<username\>@\<server name\>**
      + where the **server name** is the full path to the machine where you are running docker
    - Also type:\
       **ssh -L 8096:localhost:8096 \<username\>@\<server name\>**
      + note that we are using the port numbers defined in compose.yaml
     
Modify telegraf.conf
  * change the endpoint IP address. The default is 192.168.0.117 with port 4841

Start grafana (and influxDB and telegraf) for the first time\
  * **docker compose up -d**
    
Start a web browser and navigate to the following address:
  * localhost:8096
  * Click on "Get Started"
  * Fill in Username (i.e. admin)
  * Fill in Password
  * Fill in Initial Organization Name (i.e. SRTM)
  * Fill in Initial Bucket Name (i.e. SRTM-bucket)
  * Click "Continue"
  * Copy the API token

Stop the docker container\
  * **docker compose down**

Modify the .env file
  * Set the INFLUXDB_TOKEN to be the token created when you started influxDB for the first time.
  * Set the INFLUXDB_BUCKET to be the bucket you created from influxDB (default: SRTM-bucket)
  * Set the INFLUXDB_ORG to be the organization you set up in influcDB (default: SRTM)

Set the permissions on the grafana directory (not sure why this is needed)
  * **sudo chmod 777 grafana**
 
## Running srtm-TIG

Start the docker container again and start grafana
  * Type the command: **docker compose up -d**
  * To see if srtm-TIG has successfully launched, type: **docker ps**
    - you should see three processes running: grafana, influxDB and telegraf
  * In your browser navigate to: localhost:3020
    - log onto grafana (the default username/password are: admin/admin)
    - on the next screen you can create a new password. You can keep the default password by clicking on "skip"
    - Click on the blue box "Add new data source"
    - Choose the following Query language: **Flux**
    - In the URL box type: **http://influxdb:8086**
    - In the Organization box, type the organization: **SRTM** (by default)
    - In the Token box, type the token you saved when you initiated InfluxDB
    - In the Default Bucket, type the bucket you chose: **SRTM-bucket** (by default)
    - Click on "Save & Test"
    - You should see a green banner that says: "datasource is working"

## Creating dashboards and visualizations
You first want to create a data query (there are multiple ways to do this)
  * In a browser, navigate to localhost:8086
    - Click on the fourth icon down on the left hand side of the window called Data Explorer (it looks like a graph)
    - In the window labeled "From", click on the bucket you defined (i.e. SRTM-bucket)
    - There should be a second window called filter. Click on the "_measurement" option, and check the box next to "srtm".
    - You should see another filter window called "_field". Click on any number of variables you would like to examine. i.e. FPGA_temp
    - You should see your data displayed in the top window.
    - Click on the "Script Editor" button in the middle of the window
    - This should display the query script that you can use in grafana. Copy and paste this text.
   
Now navigate (or open another browser window) to localhost:3020
 * On the left hand side click on "Dashboards".
   - Click on the blue button "+Create dashboard"
   - Click on the blue button "+ Add visualization"
   - On the Select data source panel, click on "influxdb"
   - You can change the title of this panel using the options on the right hand side of the window, "Title"
   - At the bottom of the page you will see a number (usually 1) followed by a box. Paste your query into this box.
   - Click on the "Query inspector" button and click on "Refresh". This will run your query and show the data associated with the query.
   - Click on the "Save dashboard" button to add this visualization to the dashboard.
   - Clicking on the "Back to dashboard" button at the top of the page will allow you to see the completed panel.

You may find that the legend titles are very long. 
 * In the Grafana edit panel
 * On the rhs go to the bottom and "Add field override"
 * Choose "Fields with name" option
 * Choose the label you want to change in the "Fields with name" box
 * Choose the override property "Standard options > Display name"
 * Type in the new legend name

## Stopping srtm-TIG
In the same directory as srtm-TIG, type:
  * **docker compose down**


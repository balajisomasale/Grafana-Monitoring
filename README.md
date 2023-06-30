# Grafana-Monitoring
contains Handy notes and Hands-on exercises

### What is Grafana?

Grafana is an `open-source data visualization` and `monitoring tool` used to analyze and visualize data from various sources.
- It supports a wide range of data sources, including databases, cloud services, and time series databases like Prometheus and InfluxDB.

Data Sources:

- Grafana supports a variety of data sources, such as Prometheus, InfluxDB, Elasticsearch, MySQL, and more.
- Configure your data source(s) in Grafana by providing connection details and credentials.
- Each data source has specific query options and settings, so consult the official documentation for your chosen data source.

Dashboards:

- Dashboards in Grafana are the visual representation of your data.
- Create a new dashboard and add panels to display different metrics and visualizations.
- Choose the appropriate panel type based on the type of data you want to display (e.g., Graph, Table, Gauge).
- Customize the panel settings, including the query, time range, and visualization options.

Queries and PromQL:

- Grafana uses a query language called PromQL (Prometheus Query Language) for time series data.
- Learn PromQL to write queries that retrieve and manipulate data from the connected data source.
- PromQL supports various functions, aggregations, and operators. Refer to the official Prometheus documentation for detailed information.

Alerting:

- Grafana allows you to set up alerts based on specific conditions or thresholds.
- Define alert rules and configure notification channels (e.g., email, Slack) to receive alerts when the conditions are met.
- Ensure you have a supported alerting mechanism configured, such as Prometheus Alertmanager or an external service like PagerDuty.

Templating:

- Templating enables dynamic dashboards by allowing users to select variables or parameters.
- Use template variables to create flexible and reusable dashboards that can be customized on the fly.
- Templating variables can be based on data queries, custom values, or even obtained from other dashboards.

Plugins and Extensions:

- Grafana has a rich ecosystem of plugins and extensions that provide additional functionalities and integrations.
- Explore the Grafana plugin repository for extensions like custom panels, data source connectors, and authentication providers.

-------------------------------

What is Observability? 

- Grafana provides `Observability` which may be divided into `Monitor`, `Logs`, and `Alerts`
- For `Logging`, `Loki` should be used, and `Prometheus` is not the best option as it is widely used for `time-series database`.
Diff between Grafana(Loki) and Prometheus :

- Loki and Prometheus are both open-source tools. While `Loki` is a `log aggregation tool`, `Prometheus` is a `metrics monitoring tool`.
- Loki's design is inspired by Prometheus but for `logs`. 
- Prometheus collects `metrics` whereas `Grafana` is used as a `Datastore`

Grafana workflow : 
- `Promtail` captures and delivers to `Loki` and `Loki` routes those to `Grafana`

  `Promtail` --> `Loki` ---> `Grafana`

## Hands-on Monitoring and Visualization Project:

Create an AWS EC2 instance(Ubuntu,t2 micro,security:allow all) 

![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/f05d1c61-a646-4a9e-8e2c-be09bd4f318c)

Installation: run the below commands

https://docs.google.com/document/d/1kHhk2LYss_c2pbBCCSXo4sqaqwNF3bj_MIiqm8L5b1U/edit

Note: Install the `stable release version`  in the above doc.


Before installing `Loki` and `Promtail`,

run the `grafana-server`: 
```
sudo systemctl start grafana-server

sudo systemctl enable grafana-server
```
- Grafana usually runs on port `3000`, we can enable that in SG/inbound:

  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/31859d8f-07cd-4eaf-9972-2a687b1e5ad8)

- To open the Grafana-server, open: public_ip_address:3000
- Default username and password for grafana are : `admin`, `admin`

  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/e29cc80c-b9b3-4580-8f50-4513a57fb5f5)

  Add the data source :

  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/fa1a83d8-6038-4431-bff0-ea6704330864)

Now, we can integrate with Loki, Prometheus, or anything based on use case.

For Grafana with Loki, we need to install `Loki and promtail` using docker.

```
# Install Docker

sudo apt-get install docker.io -y
```
![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/f4bda3fa-bff4-42da-ae85-c7ccc1cf6675)

give permissions to the current user : `sudo usermod -aG docker $USER`

Note: if it does not work, restart the server and it will work.

```

# Download Loki Config

wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/cmd/loki/loki-local-config.yaml -O loki-config.yaml

# Run Loki Docker container

docker run -d --name loki -v $(pwd):/mnt/config -p 3100:3100 grafana/loki:2.8.0 --config.file=/mnt/config/loki-config.yaml

```
![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/fb551f0d-a66d-41f9-8dbd-b5a6ba97527d)

`Loki docker container` is running on `3100` 

- add port `3100` to SG/inbound 
  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/a8cf39ce-df7c-4b49-b29c-96ec60b47176)

- Go to the URL: -> public_ip_address:3100/ready

```
Download Promtail Config

wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/clients/cmd/promtail/promtail-docker-config.yaml -O promtail-config.yaml

Run Promtail Docker container

docker run -d --name promtail -v $(pwd):/mnt/config -v /var/log:/var/log --link loki grafana/promtail:2.8.0 --config.file=/mnt/config/promtail-config.yaml

# carefully observe the above command, as there is `--link` that is used to connect from Promtail to Loki so that it can share the info and then
# Loki can send those logs to Grafana 

```
![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/ed4fab3a-5707-4e6a-a9ee-da15f3b429a3)

## Adding Datastore in Grafana Dashboard:

- Make sure both docker containers are running and linked up correctly from promtail to loki
- once it is done, open the Grafana dashboard : `public_ip_address:3000` and add the datasource : Loki 
- Add the localhost address of Loki : `http://localhost:3100`
  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/18634a60-39de-48f7-9008-3a0959fb7882)

- Once we hit `explore`, we will get:

  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/8e457d46-36d3-4f09-9d0b-658ce5e8c66d)

- select the jobs and run the query :
  
  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/e1c463cb-0720-41d2-b068-13ad5f28d735)

- How and from where it is working ?

  - The path and jobs are given in `promtail.yml` that we pulled intially

    ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/04b8fcc4-1947-460e-8d2f-3dd36067a4d6)

  - we can go to that job path: `/var/log/grafana`

    ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/f395170a-a04a-407a-9506-305b5f705680)
 
    we can see all the data but not formatted.
    ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/4f4f4696-430c-46ec-95c1-5ee763172d1a)

### Creating a job for Promtail to send data to Loki:

- update the `promtail.yml` with a new label,job and location so that it can pick data from this location
  
```
  - targets:
      - localhost
    labels:
      job: balaji_grafana_logs
      __path__: /var/log/grafana/*log
```
- we need to restart the promtail docker container: `docker restart container_id`

  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/cab2db8e-5ca6-4d63-b0ff-ff3cf33fb3f5)

## Creating a Grafana Dashboard : 

- once the data/logs are flowing to grafana UI, simply click `Add to Dashboard`

  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/4178a24c-60c5-430e-be87-685417fde897)

- we can choose to visualize and other:

  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/68d487e8-a312-46a9-8d8c-c795b923f4d6)

  ![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/e0993173-b7a0-452d-b5c2-d668ffa34575)

--------------------------------------
## Grafana Project: 

Objective: Generate a graph that shows the number of words `Nginx` is repeated.

### step 1: Install Nginx server:

```
  sudo apt-get install nginx
```
- Go back to the Grafana dashboard, add a filter with `operation>range functions> rate` as below:
![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/20fec113-5725-4669-96cb-20617a272db5)

![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/44d46da8-6127-4d41-9eb3-9d314a41967e)

Adding more visualisations : 
- Add `error` in the field and use `operations > sum `

![image](https://github.com/balajisomasale/Grafana-Monitoring/assets/35003840/6b05d82f-a021-4219-a098-80a5270f75c6)


---- EOF --- 


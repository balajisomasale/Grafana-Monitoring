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
Diff between Grafana and Prometheus :

- Prometheus collects `metrics` whereas `Grafana` is used as a `Datastore`

--- stopped 3 min ---

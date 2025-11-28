# Monitoring with Prometheus and Grafana
If you use Prometheus and Grafana for metrics storage and data visualization, Solr provides 2 solutions to collect metrics and other data

 - Prometheus Exporter
 - Metrics API with Prometheus format

   
The Prometheus exporter is included with the full Solr distribution, and is located under ` prometheus-exporter `/. It is not included in the slim Solr distribution.
A Prometheus exporter (`solr-exporter`) allows users to monitor not only Solr metrics which come from the [Metrics API](https://solr.apache.org/guide/solr/latest/deployment-guide/metrics-reporting.html#metrics-api), but also facet counts which come from [Faceting](https://solr.apache.org/guide/solr/latest/query-guide/faceting.html) and responses to [Collections API](https://solr.apache.org/guide/solr/latest/configuration-guide/collections-api.html) commands and [Ping](https://solr.apache.org/guide/solr/latest/deployment-guide/ping.html) requests.

The Metrics API provides a Prometheus Response Writer to output Solr metrics natively to be scraped. It is more efficient and drops the need of running the Prometheus Exporter but at the cost of a fixed output and not as flexible in terms of configurability.


```scala
val x = 2

```
## Prometheus Exporter

There are three aspects to running solr-exporter:

 - Modify the `solr-exporter-config.xml` to define the data to collect. Solr has a default configuration you can use, but if you would like to modify it before running the exporter the first time.

 - Start the exporter from within Solr..

 - Modify your Prometheus configuration to listen on the correct port.

## Starting the server

You can start `solr-exporter` by running `./bin/solr-exporter` (Linux) or `.\bin\solr-exporter.cmd` (Windows) from the `prometheus-exporter/` directory.
The metrics exposed by `solr-exporter` can be seen at the metrics endpoint: http://localhost:8983/solr/admin/metrics.

See the commands below depending on your operating system and Solr operating mode: Only Linux shown here

### Linux


**User-managed / Single-node**

```bash
$ cd prometheus-exporter

$ ./bin/solr-exporter -p 9854 -b http://localhost:8983/solr  --config-file ./conf/solr-exporter-config.xml  --num-threads 8
```
**SolrCloud**

```bash
$ cd prometheus-exporter

$ ./bin/solr-exporter -p 9854 -z http://localhost:2181/solr --config-file ./conf/solr-exporter-config.xml --num-threads 16
```

## Prometheus Configuration


Prometheus is a separate server that you need to download and deploy. More information can be found at the Prometheus [Getting Started](https://prometheus.io/docs/prometheus/latest/getting_started/) page.
In order for Prometheus to know about the `solr-exporter`, the listen address must be added to the Prometheus server’s `prometheus.yml` configuration file, as in this example:

```bash
   scrape_configs:
     - job_name: 'solr'  
       static_configs:
         - targets: ['localhost:9854']
```

If you already have a section for `scrape_configs`, you can add the `job_name` and other values in the same section.

When you apply the settings to Prometheus, it will start to pull Solr’s metrics from `solr-exporter`.

You can test that the Prometheus server, `solr-exporter`, and Solr are working together by browsing to http://localhost:9090 and doing a query for `solr_ping` metric in the Prometheus GUI:

## Sample Grafana Dashboard

To use Grafana for visualization, it must be downloaded and deployed separately. More information can be found on the Grafana [Documentation] (https://grafana.com/docs/grafana/latest/) site.

A Grafana sample dashboard is provided in the following JSON file: `prometheus-exporter/conf/grafana-solr-dashboard.json`. You can place this with your other Grafana dashboard configurations and modify it as necessary depending on any customization you’ve done for the `solr-exporter` configuration.

You can directly import the Solr dashboard [via grafana.com](https://grafana.com/grafana/dashboards/12456-solr-dashboard/) by using the Import function with the dashboard id 12456. 


***

***

***

# An Implimentation

In this tutorial we will give a practical example of running the `solr-exporter` in a SolrCloud cluster.  
We will use a three node SolrCloud cluster with a Data Historian Collection and a Zookeeper ensemble (of three) and push metrics to Prometheus using the `solr-exporter`. Finally we will visualize the operating system, metrics pulled by Prometheus, listening on http://localhost:9090 and the `solr-exporter` metrics listening on http://localhost:9854 via Grafana using some prebuilt dashboards.


solr dashboard

<img width="1280" height="1024" alt="snapshot1Nov18" src="https://github.com/user-attachments/assets/2e3097d5-455a-494d-80d6-ea855fd64d93" />


solrCloud mode


<img width="1680" height="1050" alt="snapshot8June15" src="https://github.com/user-attachments/assets/d50cd6e2-9e32-4622-9c73-379956d6bd28" />

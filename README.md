# elasticon_tour16_demo

This repo contains setup for Elasticon Tour 16 Kibana demo. 

Based on the following examples:

https://github.com/elastic/examples/tree/master/ElasticStack_apache

https://github.com/kosho/ncedc-earthquakes


# Demo Setup

This section walks you through setup of three datasets - “earthquake data”, “apache logs”, and “log search”.

## Elastic Stack Setup
* Download and install Elasticsearch, Kibana, and Logstash versions 5.0.0.
* Install x-pack.

## General setup
* Download all files from this repo: https://github.com/tbragin/elasticon_tour16_demo
* Put all files in the Logstash directory

## Earthquake Data
* Extract the dataset archive with `tar zxf ncedc-earthquakes-dataset.tar.gz`
* Modify `ncedc-earthquakes-logstash.conf` with Elasticsearch URL and credentials using command:
`cat earthquakes.txt| bin/logstash -f ncedc-earthquakes-logstash.conf`
* In Kibana, createa  `ncedc-earthquakes` index pattern
* In Timelion, create a new dashboard and add these three visualizations
* Save dashboard with a “Last 20 years” time range

`.es(index=ncedc-earthquakes,metric=avg:depth).points(fill=5).color(#8cd98c).label('Depth - raw').yaxis(label="Depth"),.es(index=ncedc-earthquakes,metric=avg:depth).lines().movingaverage(52).label('Depth - moving avg').yaxis(label="Depth").color(#257425 ), .es(index=ncedc-earthquakes,metric=avg:mag).points(fill=5).label('Magnitude - raw').yaxis(2, label="Magnitude").color(#ff6666), .es(index=ncedc-earthquakes,metric=avg:mag).lines().movingaverage(52).label('Magnitude - moving avg').yaxis(2, label="Magnitude").color(#b30000 )`

`.es(index=ncedc-earthquakes,metric=avg:mag).points(fill=5).label('raw').yaxis(2, label="Magnitude").color(#ffa3a3), .es(index=ncedc-earthquakes,metric=avg:mag).lines().movingaverage(52).label('5 year moving avg').yaxis(2, label="Magnitude").color(#b30000 ), .es(index=ncedc-earthquakes,metric=avg:mag).lines().movingaverage(10).label('1 year moving avg').yaxis(2, label="Magnitude").color(#ff3333)`

`.es(index=ncedc-earthquakes,metric=avg:depth).points(fill=5).color(#a3e0a3).label('raw').yaxis(label="Depth"),.es(index=ncedc-earthquakes,metric=avg:depth).lines().movingaverage(52).label('5 year moving avg').yaxis(label="Depth").color(#257425 ), .es(index=ncedc-earthquakes,metric=avg:depth).lines().movingaverage(10).label('1 year moving avg').yaxis(label="Depth").color(#00b359)`



## Apache Logs
* Modify apache_logstash.conf with Elasticsearch URL and credentials using command:
`cat apache_logs| bin/logstash -f apache_logstash.conf `
* In Kibana, create `apache_elk_example` index pattern
* Upload visualizations and dashboards from JSON files you downloaded
* Save dashboard with an appropriate time range 


## Log Search

* Modify logs_small.conf with Elasticsearch URL and credentials using command:
`cat logs_small | bin/logstash -f logs_small.conf`
* In Kibana, create `logstash-*` index pattern
* Save a search with * named “Log Search”

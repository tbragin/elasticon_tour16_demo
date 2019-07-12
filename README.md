# elasticon_tour16_demo

This repo contains setup for Elasticon Tour 16 Kibana demo.

Based on the following examples:

https://github.com/elastic/examples/tree/master/ElasticStack_apache

https://github.com/kosho/ncedc-earthquakes


# Demo Setup

This section walks you through setup of three datasets - “earthquake data”, “apache logs”, and “log search”.

## Elastic Stack Setup

### Setup with Docker

This uses the official docker images for ELK version 7.2.0.

#### System requirements

* `docker`
* `docker-compose`

#### Start `elasticsearch` and `kibana`

```
$ docker-compose up -d kibana
```

You can monitor logs with `docker logs -f [kibana|es0]`.

The kibana application is eventually accessible at http://localhost:5601.

#### Earthquake data

* Extract the dataset archive with `tar zxf data/ncedc-earthquakes-dataset.tar.gz -Cdata`
* Index the data to `elasticsearch` through `logstash`:

  ```
  $ docker-compose up -d logstash
  ```

  This is equivalent to
  ```
  $ docker-compose run logstash /bin/bash -c \
      "cat data/earthquakes.txt | bin/logstash -f pipeline/ncedc-earthquakes-logstash.conf"
  ```

* In Kibana, perform the following operations through Management:
  * Create an index pattern with the name `ncedc-earthquakes`.
  * Create visualizations. There are two ways to do this:

    * Either import the saved-objects from
      `ELK7/kibana-objects/earthquakes_dashboard.ndjson`, or
    * Create the visualizations from scratch through Timelion
      on a new dashboard, following the instructions in the system-wide
      implementation (see [here](#original-eqdata))

      > **NOTE**: Set the time-range for the `Last 20 years`.

#### Apache logs

* Index the data to `elasticsearch` through `logstash`:

  ```
  $ docker-compose run logstash /bin/bash -c \
      "cat data/apache_logs | bin/logstash -f pipeline/apache-logstash.conf"
  ```

* In Kibana, perform the following operations through Management:
  * Create an index pattern with the name `apache-elk-example`.
  * Create visualizations by importing
      `ELK7/kibana-objects/webanalytics_dashboard.ndjson`.

#### Log search

* Index the data to `elasticsearch` through `logstash`:

  ```
  $ docker-compose run logstash /bin/bash -c \
      "cat data/logs_small | bin/logstash -f pipeline/logs_small.conf"
  ```
* In Kibana, create `logstash-*` index pattern
* Save a search with * named “Log Search”

See also the system-wide implementation ([here][#original-logs-small])

### System-wide setup

* Download and install Elasticsearch, Kibana, and Logstash, and X-Pack: https://www.elastic.co/start
* This setup was tested with 5.0.x and 5.1.x

#### General setup
* Download all files from this repo: https://github.com/tbragin/elasticon_tour16_demo
* Put all files in the Logstash directory

<a name="original-eqdata"></a>
#### Earthquake Data
* Extract the dataset archive with `tar zxf data/ncedc-earthquakes-dataset.tar.gz -Cdata`
* Modify `pipeline/ncedc-earthquakes-logstash.conf` with Elasticsearch URL and credentials using command:
`cat data/earthquakes.txt| bin/logstash -f ELK5/pipeline/ncedc-earthquakes-logstash.conf`
* In Kibana, create a  `ncedc-earthquakes` index pattern
* In Timelion, create a new dashboard and add these three visualizations
* Save dashboard with a “Last 20 years” time range

`.es(index=ncedc-earthquakes,metric=avg:depth).points(fill=5).color(#8cd98c).label('Depth - raw').yaxis(label="Depth"),.es(index=ncedc-earthquakes,metric=avg:depth).lines().movingaverage(52).label('Depth - moving avg').yaxis(label="Depth").color(#257425 ), .es(index=ncedc-earthquakes,metric=avg:mag).points(fill=5).label('Magnitude - raw').yaxis(2, label="Magnitude").color(#ff6666), .es(index=ncedc-earthquakes,metric=avg:mag).lines().movingaverage(52).label('Magnitude - moving avg').yaxis(2, label="Magnitude").color(#b30000 )`

`.es(index=ncedc-earthquakes,metric=avg:mag).points(fill=5).label('raw').yaxis(2, label="Magnitude").color(#ffa3a3), .es(index=ncedc-earthquakes,metric=avg:mag).lines().movingaverage(52).label('5 year moving avg').yaxis(2, label="Magnitude").color(#b30000 ), .es(index=ncedc-earthquakes,metric=avg:mag).lines().movingaverage(10).label('1 year moving avg').yaxis(2, label="Magnitude").color(#ff3333)`

`.es(index=ncedc-earthquakes,metric=avg:depth).points(fill=5).color(#a3e0a3).label('raw').yaxis(label="Depth"),.es(index=ncedc-earthquakes,metric=avg:depth).lines().movingaverage(52).label('5 year moving avg').yaxis(label="Depth").color(#257425 ), .es(index=ncedc-earthquakes,metric=avg:depth).lines().movingaverage(10).label('1 year moving avg').yaxis(label="Depth").color(#00b359)`

<img width="1390" alt="screen shot 2017-01-05 at 10 22 57 pm" src="https://cloud.githubusercontent.com/assets/933397/21910274/25f24142-d8d0-11e6-9ebd-cf9b94edbfe9.png">

#### Apache Logs
* Modify `ELK5/pipeline/apache_logstash.conf` with Elasticsearch URL and credentials, and start `logstash` with:
`cat data/apache_logs| bin/logstash -f ELK5/pipeline/apache_logstash.conf `
* In Kibana, create `apache_elk_example` index pattern
* Upload visualizations and dashboards from JSON files in `ELK5/kibana-objects`
* Save dashboard with an appropriate time range

<img width="1388" alt="screen shot 2017-01-05 at 10 23 14 pm" src="https://cloud.githubusercontent.com/assets/933397/21910273/25f1845a-d8d0-11e6-9afc-bbdde45c5f03.png">

<a name="original-logs-small"></a>
#### Log Search

* Modify `ELK5/pipeline/logs_small.conf` with Elasticsearch URL and credentials, and start `logstash` with:
`cat data/logs_small | bin/logstash -f /ELK5/pipeline/logs_small.conf`
* In Kibana, create `logstash-*` index pattern
* Save a search with * named “Log Search”

<img width="1383" alt="screen shot 2017-01-05 at 10 23 50 pm" src="https://cloud.githubusercontent.com/assets/933397/21910270/25ed0380-d8d0-11e6-9a67-e941530ed925.png">

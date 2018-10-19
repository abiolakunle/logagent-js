[![Build Status](https://api.travis-ci.org/sematext/logagent-js.svg?branch=master)](https://travis-ci.org/sematext/logagent-js)
[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/sematext/logagent-js) - [read more](http://blog.sematext.com/2016/02/18/how-to-ship-heroku-logs-to-logsene-managed-elk-stack/)

# What is Logagent

Logagent is a modern, open-source, light-weight log shipper. It is like Filebeat and Logstash in one, without the JVM memory footprint.  It comes with out of the box and extensible log parsing, on-disk buffering, secure transport, and bulk indexing to Elasticsearch, Logsene, and other destinations. Its low memory footprint and low CPU overhead make it suitable for deploying on edge nodes and devices, while its ability to parse and structure logs makes it a great Logstash alternative.

![](https://sematext.com/wp-content/uploads/2016/07/logagent.png)

# Docker

You can run Logagent in a container. The docker container can be configured through the following environment variables:

* **LOG_URL**: The URL of your Elasticsearch Endpoint _(defaults to https://logsene-receiver.sematext.com)_
               For Sematext Europe use https://logsene-receiver.eu.sematext.com. For Elasticsearch https://elasticserch-server-name:9200.
* **LOG_INDEX**: The index where the agent should log to _(for sematext users the logs token)_
* **LOG_GLOB**: Semicolon-separated list of file globs _(e.g. /var/log/**/*.log;/my/app/logs/*.log)_
* **LA_ARGUMENTS**: Additional [command line arguments for Logagent](https://sematext.com/docs/logagent/cli-parameters/) _(e.g. LA_ARGUMENTS="-n httpd" to specify a log source name or LA_ARGUMENTS="-u 514" to act as syslog server)_

Run a container:
The following example enables UDP syslog receiver and ships all log files from /var/log to Sematext Cloud (US).
Simply mount the log file directory into Logagent container and provide a glob pattern to match the log files to watch.

```
docker run -d --name logagent \
-v /var/log:/mylogs
-p 1514:514/udp \
-e LOG_URL=https://logsene-receiver.sematext.com \
-e LOG_INDEX=YOUR_LOGSENE_TOKEN_HERE \
-e LOG_GLOB="/mylogs/**/.log" \
-e LA_ARGUMENTS="-u 514" \
sematext/logagent
```

# Installation

**1) Install Node.js**

Official Node.js [downloads and instructions](https://nodejs.org/en/download/). E.g. for Debian/Ubuntu:

```
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
Install Logagent with npm
sudo npm i -g @sematext/logagent
```

**2) Run logagent command line tool**

```
logagent --help
```

**3) Example: Index your log files in Elasticsearch**

```
logagent -e http://localhost:9200 -i logs -g ‘/var/log/**/*.log’
```

**4) Optional: Install service & config**

Install service for Logagent using systemd, upstart, launchd
To quickly create a config file for indexing into Elasticsearch without having to edit it run something like this:

```
sudo logagent-setup -u http://localhost:9200 -i INDEX_NAME -g '/var/log/**/*.log'
# Logsene US: use -u https://logsene-receiver.sematext.com and your Logsene App Token as index name.
# Logsene EU: use -u https://logsene-receiver.eu.sematext.com and your Logsene App Token as index name.
```

**Configuration**

To configure different inputs, different event processing, or different outputs (e.g. your own Elasticsearch) edit /etc/sematext/logagent.conf, e.g.:

```
output:
  logsene:
    module: elasticsearch
    url: http://elasticsearch-server:9200
    index: logs
```

Then restart the service with sudo service logagent restart.
Troubleshooting & Logs
Logagent’s own logs:

* Upstart: `/var/log/upstart/logagent.log`
* Systemd: `journalctl -u logagent`
* Launchd: `/Library/Logs/logagent.log`

Location of service scripts:

* Upstart: `/etc/init/logagent.conf`
* Systemd: `/etc/systemd/system/logagent.service`
* Launchd: `/Library/LaunchDaemons/com.sematext.logagent.plist`

Start/stop service:

* Upstart: `service logagent stop/start`
* Systemd: `systemctl stop/start logagent`
* Launchd: `launchctl start/stop com.sematext.logagent`

# Documentation & Support

* [Full documentation](http://sematext.com/docs/logagent/)
* [Logagent main page](https://sematext.com/logagent)
* [Logagent on Github](https://github.com/sematext/logagent-js)
* Forum: [https://groups.google.com/forum/#!forum/logagent](https://groups.google.com/forum/#!forum/logagent)
* Twitter: [@sematext](https://twitter.com/sematext)
* Blog: [sematext.com/blog](https://sematext.com/blog)

# Development

* Update to the last node version
* From root folder node type: node ./bin/logagent -h
* To test from root folder type: node test

::wink

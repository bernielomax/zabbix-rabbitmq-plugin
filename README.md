# zabbix-rabbitmq-plugin
A Zabbix plugin for monitoring RabbmitMQ

## Installation

This plugin can be setup and run from the Zabbix server or on the RabbitMQ server itself.


### 1. Download the source to your Zabbix scripts path

**Example path: /etc/zabbix/scripts**

``` 
cd /etc/zabbix/scripts
git clone https://github.com/bernielomax/zabbix-rabbitmq-plugin.git
```

### 2. Setup the zabbix_agentd.d file

##### Copy the zabbix-rabbitmq.conf file to the "zabbix_agentd.d" folder
```
cp zabbix_agentd.d/zabbix-rabbitmq.conf /etc/zabbix/zabbix_agentd.d/
```

##### Edit the file to make sure that the Zabbix path is correct
```
UserParameter=rabbitmq.discover.nodes[*],/etc/zabbix/scripts/zabbix-rabbitmq-plugin/zabbix-rabbitmq-plugin --hostname $1 discover --cluster-nodes
UserParameter=rabbitmq.discover.queues[*],/etc/zabbix/scripts/zabbix-rabbitmq-plugin/zabbix-rabbitmq-plugin --hostname $1 discover --queues
UserParameter=rabbitmq.check.queue[*],/etc/zabbix/scripts/zabbix-rabbitmq-plugin/zabbix-rabbitmq-plugin --hostname $1 check queue --vhost $2 --name $3 --metric $4
UserParameter=rabbitmq.check.node[*],/etc/zabbix/scripts/zabbix-rabbitmq-plugin/zabbix-rabbitmq-plugin --hostname $1 check node --name $2 --metric $3
```

### 3. Restart Zabbix

```
service zabbix restart
```

### 3. Set the plugin configuration

The `config.yml` contains the settings used to connect to the RabbitMQ server. Below explains how to setup your config file.

##### The "Defaults" section

The defaults section contains the settings to be applied to all hosts.

##### The "Hosts" section

The hosts section is used to override the default settings for any particular host defined.

##### Authentication

Username & Password as you would expect.

##### TTL

How long to store retrieved API metrics in the cache for.

##### Port

The port that the RabbitMQ API is listening on

```
hosts:

  #myhost:
  #  username: myuser
  #  password: mypass

defaults:
    username: bernie
    password: itsjustbernie!
    port: 15672
    ttl: 45

```

### 4. Import the template

The `zabbix-rabbitmq-template.xml` file contains some basic discovery items, triggers and graphs. The template can be imported by going to the ***"Configuration/Templates"*** page in the Zabbix web console and by clicking the ***"import"*** button. 

**IMPORTANT** After importing the template make sure you set the the RabbitMQ server hostname on both of the newly imported discovery rules.

## Adding custom items for monitoring

You can add additional custom items to be monitored. This plugin retrieves all information from the from the API call. It then flattens the JSON response into a flattened "dot" separated key value store.

##### For example "/api/queues" returns

```

```

#####An example of the keys available to Zabbix would be

```

```

## The cache schema

The cache resides in a SQLite file called `zabbix-rabbitmq-cache.db`. Below is an example of the schema.

```
CREATE TABLE CACHE(
   ID INTEGER PRIMARY KEY,
   HOSTNAME           CHAR(50) NOT NULL,
   METHOD         CHAR(50) NOT NULL,
   DATA           TEXT, 
   TIMESTAMP      INT      NOT NULL
);
```

## License

```
MIT License

Copyright (c) 2016 Justin Pye

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

```
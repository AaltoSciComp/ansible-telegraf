dj-wasabi.telegraf
=========

This role will install and configure telegraf.

Telegraf is an agent written in Go for collecting metrics from the system it's running on, or from other services, and writing them into InfluxDB.

Design goals are to have a minimal memory footprint with a plugin system so that developers in the community can easily add support for collecting metrics from well known services (like Hadoop, Postgres, or Redis) and third party APIs (like Mailchimp, AWS CloudWatch, or Google Analytics).

(https://github.com/influxdb/telegraf)

Requirements
------------

No requirements. (Yes, an Influxdb server somewhere on the network will help though ;-) )

Role Variables
--------------

The following parameters can be set for the Telegraf agent:

* `telegraf_agent_version`: The version of Telegraf to install. Default: `0.1.9`
* `telegraf_agent_interval`: The interval configured for sending data to the server. Default: `10`
* `telegraf_agent_debug`: Setting the Telegraf in debug mode. Default: `False`
* `telegraf_agent_utc`: Option for outputting data in UTC. Default: `True`
* `telegraf_agent_precision`: Precision to write data at. Valid values for Precision are n, u, ms, s, m, and h. Default: `s`

You can set tags for the host running telegraf:

	telegraf_agent_tags:
	  - tag_name: some_name
	    tag_value: some_value

Specifying an output. The default is set to localhost, you'll have to specify the correct influxdb server:

	telegraf_agent_output:
	  - type: influxdb
	    config:
	      - url = "http://localhost:8086"
	      - database = "telegraf"

The config will be printed line by line into the configuration, so you could also use:

	config:
		- # Print an documentation line

and it will be printed in the configuration file.

There are two properties which are the same, but are used differently. Those are:

* `telegraf_plugins_default`
* `telegraf_plugins_extra`

With the property `telegraf_plugins_default` it is set to use the default set of Telegraf plugins. You could override it with more plugins, which should be enabled at default.

	telegraf_plugins_default:
	  - plugin: cpu
	    config:
	      - percpu = true
	  - plugin: disk
	  - plugin: io
	  - plugin: mem
	  - plugin: system
	  - plugin: swap

Every telegraf agent has these as an default configuration.
 
The 2nd parameter `telegraf_plugins_extra` can be used to add plugins specific to the servers goal. Following is an example for using this parameter for MySQL database servers:

	cat group_vars/mysql_database
	telegraf_plugins_extra:
		- plugin: mysql
		  config:
		  	- servers = ["root:{{ mysql_root_password }}@tcp(localhost:3306)/"]


Telegraf plugin options:

* `pass`: An array of strings that is used to filter metrics generated by the current plugin. Each string in the array is tested as a prefix against metric names and if it matches, the metric is emitted.
* `drop`: The inverse of pass, if a metric name matches, it is not emitted.
* `tagpass`: (added in Telegraf 0.1.5) tag names and arrays of strings that are used to filter metrics by the current plugin. Each string in the array is tested as an exact match against the tag name, and if it matches the metric is emitted.
* `tagdrop`: (added in Telegraf 0.1.5) The inverse of tagpass. If a tag matches, the metric is not emitted. This is tested on metrics that have passed the tagpass test.
* `interval`: How often to gather this metric. Normal plugins use a single global interval, but if one particular plugin should be run less or more often, you can configure that here.

An example might look like this:

	telegraf_plugins_default:
	  - plugin: disk
	    interval: 12
	    tagpass:
	      - fstype = [ "ext4", "xfs" ]
    	  - path = [ "/opt", "/home" ]



Dependencies
------------
No dependencies

Example Playbook
----------------

    - hosts: servers
      roles:
         - { role: dj-wasabi.telegraf }

Test Kitchen
------------

This roles is configured to be tested with Test Kitchen. You can find on this page some more information regarding Test Kitchen: http://werner-dijkerman.nl/2015/08/20/using-test-kitchen-with-docker-and-serverspec-to-test-ansible-roles/

License
-------

BSD

Author Information
------------------

Please let me know if you have issues. Pull requests are also accepted! :-)

mail: ikben [ at ] werner-dijkerman . nl

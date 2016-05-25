# Graphite module for Icinga Web 2

## General Information

Enable the graphite carbon cache writer: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/monitoring-basics#graphite-carbon-cache-writer

All perfdata metrics will be automatically included as graphs however if you just want a subset, the host or service then needs to have custom vars of the form vars.graphite_keys =["key1","key2"] where key1 key2 represent perfdata stats you want to see.

You can configure the graphite metric keys formats by using standard-ish icinga2 macros.

## Installation

Just extract this to your Icinga Web 2 module folder in a folder called graphite.

(Configuration -> Modules -> graphite -> enable). Check the modules config tab right there.

NB: It is best practice to install 3rd party modules into a distinct module
folder like /usr/share/icingaweb2/modules. In case you don't know where this
might be please check the module path in your Icinga Web 2 configuration.

##Configuration
There are various configuration settings to tweak how the module behaves and ensure that it aligns with how the graphite carbon cache writer is set up:

``base_url``
A fully formed url
* *http://graphite.com/render?*

``legacy_mode``
To support older versions of the writer pre-icinga2 2.4 where true - is replaced with _
* *false*

``service_name_template``
Macro template for the service name
* *icinga2.$host.name$.services.$service.name$.$service.check_command$.perfdata.$metric$.value*

``host_name_template``
Macro template for the host name
* *icinga2.$host.name$.host.$host.check_command$.perfdata.$metric$.value*

``graphite_args_template``
Macro template for the small image where $target$ is replaced with the metric name
* *&target=$target$&source=0&width=300&height=120&hideAxes=true&lineWidth=2&hideLegend=true&colorList=049BAF*

``graphite_large_args_template``
Macro template for the large image
* *&target=$target$&source=0&width=800&height=700&colorList=049BAF&lineMode=connected*

## Customizing
As mentioned in General Information above, you can use vars.graphite_keys to limit [or define] the graphs you want to see.

### Limiting graphs
In our example there are 6 metrics available in perfdata from the check_tomcat script:
* used
* free
* max
* currentThreadCount
* maxThreads
* currentThreadsBusy

To just see the basic graphs of used heap and currentThreadCount, add the following to your Service definition.

    vars.graphite_keys = ["used", "currentThreadCount"]

### Composite Graphs
In some cases, composite graphs are more useful.  Used Heap doesn't make sense unless you know the Max Heap size.  To create some useful composite graphs, add the following to your Service definition.

    vars.graphite_keys = ["{used,max}", "{currentThreadsBusy,currentThreadCount}"]

### Graph Labels
Composite graph definitions can get long and cumbersome, so you can define Labels for each graph.  To make short, user-friendly labels for the composite graphs, add the following to your Service definition.

    vars.graphite_keys = ["{used,max}", "{currentThreadsBusy,currentThreadCount}"]
    vars.graphite_labels = ["Heap", "Threads"]

### Area mode
It is possible to fill the area below the graphed lines by adding the following to your Service definition.

    vars.graphite.area_mode = "all" // default : "all"
    vars.graphite.area_alpha = "0.1" // default : "0.1"

To set it globally, customize the configuration file with :

    graphite_area_mode = all
    graphite_area_alpha = 0.1

For details about the values, see doc here : http://graphite.readthedocs.io/en/latest/render_api.html?highlight=areaMode

### Derivative graphs
It is possible to plot derivative graphs instead of the measured values. This is useful for sensors giving increasing counters (like printer's page count, network IO bytes counter, ...). You can set it by adding the following to your Service definition.

    vars.graphite.graph_type = "derivative" // default : "normal"

You can customize it's output by setting the summarize interval and summarize function :

    vars.graphite.summarize_interval = "60min" // default : "10min"
    vars.graphite.summarize_func = "sum" // default : "sum"

To set it globally, customize the configuration file with :

    graphite_summarize_interval = 10min

### Color list
It is possible to define which colors to use in the graph. You can set it by adding the following to your Service definition.

    vars.graphite.color_list = "2D7DB3,EE1D00" // default : "049BAF,EE1D00,04B06E,0446B0,871E10,CB315D,B06904,B0049C"

To set it globally, customize the configuration file with :

    graphite_color_list = 2D7DB3,EE1D00

This might be specialy useful in conjunction with `vars.graphite_keys` which plots several graphs in the same picture.

## Hats off to

This module borrows a lot from https://github.com/Icinga/icingaweb2-module-pnp4nagios

## What it looks like		

![screen shot of graphite graph](https://raw.githubusercontent.com/philiphoy/icingaweb2-module-graphite/master/Capture.PNG)

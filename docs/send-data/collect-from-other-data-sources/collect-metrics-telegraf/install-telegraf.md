---
id: install-telegraf
---

# Install Telegraf

This topic has instructions for installing Telegraf to work with Sumo Logic. We provide two sets of instructions:

 * Install Telegraf in a non-Kubernetes environment
 * Install Telegraf in a Kubenetes environment

:::note
If you’re new to Telegraf, see [Telegraf Collection Architecture](telegraf-collection-architecture.md), which has an overview of Telegraf and the metric collection pipelines for both Kubernetes and non-Kubernetes environments.
:::

## Install Telegraf in a non-Kubernetes environment

This section has instructions for running Telegraf in a non-Kubernetes environment. 

## Prerequisites 

This section describes prerequisites for installing Telgraf.

## Privileges

Installing Telegraf typically requires root or administrator privileges. However, if you are using a pre-built binary, this is not the case. 

## Networking

Telegraf input plugins may require custom ports. You configure port mappings in `telegraf.conf`, which, in default Linux installations, is located in `/etc/telegraf`. In a Windows installation, the configuration file is in the directory where you unzipped the Telegraf archive, `C:\InfluxData\telegraf` by default.

### NTP

Telegraf uses a host’s local time in UTC to assign timestamps to data. Use the Network Time Protocol (NTP) to synchronize time between hosts; if hosts’ clocks aren’t synchronized with NTP, the timestamps on the data can be inaccurate.

## Get Telegraf

You need a minimum of 1.16 version of Telegraf. Download a [\>=1.16 release of](https://github.com/influxdata/telegraf/releases) Telegraf. 

## Install Telegraf on Ubuntu or Debian with apt-get

This section has instructions for installing the latest stable version of Telegraf on Ubuntu or Debian using the apt-get package manager. 

:::note
If you want to install Telegraf using a .deb file, or on Windows see [Manually install Telegraf from a .deb file](#manually-install-telegraf-on-debian-from-a-deb-file) or [Install Telegraf on Windows](#install-telegraf-on-windows). Telegraf releases are available for all Operating Systems through the portal [downloads page](https://portal.influxdata.com/downloads/).
:::

1. Add the InfluxData repository.

   * To add the repository on Ubuntu, run the following command in a terminal window. 

        ```
        wget -qO- https://repos.influxdata.com/influxdb.key | sudo apt-key add -
        source /etc/lsb-release
        echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
        ```
    
    * To add the repository on Debian, run the following commands in a terminal window, skipping the comment lines, which begin with #.

        ```
        # Before adding Influx repository, run this so that apt will be able to read the repository.

        sudo apt-get update && sudo apt-get install apt-transport-https

        # Add the InfluxData key

        wget -qO-https://repos.influxdata.com/influxdb.key | sudo apt-key add -
        source /etc/os-release
        test $VERSION_ID = "7" && echo "deb https://repos.influxdata.com/debian wheezy stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
        test $VERSION_ID = "8" && echo "deb https://repos.influxdata.com/debian jessie stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
        test $VERSION_ID = "9" && echo "deb https://repos.influxdata.com/debian stretch stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
        test $VERSION_ID = "10" && echo "deb https://repos.influxdata.com/debian buster stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
        ```

1. To install and start the Telegraf service, run the following commands in a terminal window:

    ```
    sudo apt-get update && sudo apt-get install telegraf
    sudo service telegraf start
    ```
    
    Or, if your operating system uses systemd (Ubuntu 15.04+, Debian 8+):
    
    ```
    sudo apt-get update && sudo apt-get install telegraf
    sudo systemctl start telegraf
    ```

## Manually install Telegraf on Debian from a .deb file

To manually install the Debian package from a .deb file:

1. Download the latest Telegraf .deb release from the Telegraf section of the [downloads page](https://portal.influxdata.com/downloads/).
1. Run the following command (making sure to supply the correct version number for the downloaded file): 
   
    ```
    sudo dpkg -i telegraf_1.<version>_amd64.deb
    ```

## Install Telegraf on Windows

Telegraf has native support for running as a Windows service. For additional information, see the Influx blog post [Using Telegraf on Windows](https://www.influxdata.com/blog/using-telegraf-on-windows/).

:::note
You must have administrative permissions to install a Windows service. Be sure to launch Powershell as administrator.
:::

1. Launch PowerShell as an administrator.
1. Download the Telegraf binary from the Telegraf section of the [downloads page](https://portal.influxdata.com/downloads/) and unzip its contents to `C:\Program Files\InfluxData\Telegraf`. The InfluxData [GitHub repositiory](https://github.com/influxdata/telegraf/releases) provides a list of all available releases.   You can also use the following Invoke-WebRequest PowerShell command with a specific Telegraf version (1.80.0 in this example):   

    ```
    > Invoke-WebRequest https://dl.influxdata.com/telegraf/releases/telegraf-1.80.0_windows_amd64.zip -OutFile telegraf.zip
    ```

1. In PowerShell, run these commands:   

    ```
    > cd "C:\Program Files\InfluxData\Telegraf"
    > .\telegraf.exe --service install --config "C:\Program Files\InfluxData\Telegraf\telegraf.conf"
    ```

    :::note
    Be sure to provide the absolute path to the Telegraf configuration file.
    :::

1. To test that the installation works, run:

    ```
    > C:\"Program Files"\InfluxData\Telegraf\telegraf.exe --config C:\"Program Files"\InfluxData\Telegraf\telegraf.conf --test
    ```

1. To start collecting data, run:

    ```
    telegraf.exe --service start
    ```

## Windows service logging and troubleshooting

When Telegraf runs as a Windows service, Telegraf logs messages to Windows event logs. If the Telegraf service fails to start, view error logs by selecting **Event Viewer \> Windows Logs \> Application**.

### Windows service commands

| Command | Description |
|------------------------------------|-------------------------------|
| `telegraf.exe --service install  ` | Install telegraf as a service |
| `telegraf.exe --service uninstall` | Remove the telegraf service   |
| `telegraf.exe --service start    ` | Start the telegraf service    |
| `telegraf.exe --service stop`      | Stop the telegraf service     |

## Install Telegraf in a Kubernetes environment

This section documents the steps for setting up Telegraf in a Kubernetes environment. Due to the dynamic nature of Kubernetes, we use the Telegraf Operator. 

1. First you need to set up Sumo Logic’s Kubernetes collection.

   * If you have not set up Sumo Logic’s Kubernetes collection, perform [these steps to set up Kubernetes collection](https://github.com/SumoLogic/sumologic-kubernetes-collection/blob/release-v1.2/deploy/docs/Installation_with_Helm.md#installation-with-helm). 
   
    :::note
    When installing, make sure you enable the Telegraf Operator by adding this to the installation command:  ` --set telegraf-operator.enabled=true `
    :::

   * If you have already set up Kubernetes collection, you can upgrade to the latest version and enable the Telegraf Operator.
   
    ```
    helm upgrade ... --set telegraf-operator.enabled=true ... 
    ```

1. After the Telegraf Operator pod is ready, add the following annotations to the pods from which you want to collect metrics.

    ```
    telegraf.influxdata.com/inputs: |+
        # Here goes telegraf configuration for scrapping metrics
    (nginx example)
            [[inputs.nginx]]
                urls = ["http://localhost:8080/stub_status"]
    telegraf.influxdata.com/class: sumologic-prometheus  # points to predefined output configuration (exposing metrics to prometheus, so metadata enrichment can be performed)
    prometheus.io/scrape: "true"  # Enable scrapping metrics by prometheus
    prometheus.io/port: "9273"    # Defines from which port prometheus should scrape metrics
    ```

For more details and examples, see [Configure Telegraf Input Plugins](configure-telegraf-input-plugins.md). 

## Configuring Telegraf

Telegraf supports a number of configuration options. Below is a summary of some of the most common ones. For the complete list, see [Telegraf documentation](https://github.com/influxdata/telegraf/blob/master/docs/CONFIGURATION.md).

### Adjust the collection interval

You can adjust the collection and reporting intervals in the `[agent]` block of your Telegraf configuration. The scraping interval is configured with the `interval` property, and the `flush_interval` property specifies the interval at which the data will be sent to configured outputs. Specify durations by combining an integer value and time unit as a string value. Valid time units are `ns`, `us` (or `µs`), `ms`, `s`, `m`, `h`. The default is 10  seconds.

The following example collects and send metrics to Sumo Logic every 30 seconds.

```
[agent]
  interval = "30s"
  flush_interval = "30s"
```

### Add additional metadata

You may wish to add additional metadata to the metrics that Telegraf collects. You can do so with Global Tags. Global tags can be specified in the `[global_tags]` table in key="value" format. All metrics that are collected will be tagged with the specified tags.

```
[global_tags]
  dc = "us-east-1"
```
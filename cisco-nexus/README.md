# Blue Medora Cisco Nexus Plugin for New Relic

The **Blue Medora Cisco Nexus Plugin for New Relic** allows you to monitor your Cisco Nexus performance data from within the New Relic platform by pulling in metrics from the system and displaying them in a set of intuitive, graph-based monitoring dashboards.           

This guide includes instructions for installing and configuring the Blue Medora Cisco Nexus Plugin for New Relic.

----


## Obtaining the Plugin
You can find the New Relic Cisco Nexus plugin in the following locations:

- New Relic Storefront [Here](<link TBD>)
- Plugin Central

## System Requirements

The Cisco Nexus plugin connects to the Cisco Nexus hardware (switch) using SNMP v2, v2c, or v3.  Before installing and configuring the plugin, ensure your system meets the following requirements:

**New Relic Requirements**
- A New Relic account

**Cisco Nexus Plugin Requirements**
- SNMP v2, v2c, or v3 enabled **Cisco Nexus switch(es) 1k, 3k, 4k, 5k, 7k, and/or 9k**
- **A Blue Medora License.** A trial license will ship with the plugin. This license will remain effective for the duration of the Blue Medora beta trial period.


----

## Installing the Plugin

This plugin can be installed one of the following ways:

- Using the New Relic Platform Installer
- Installing the Plugin Manually

### Using the New Relic Platform Installer

The New Relic Platform Installer (NPI) is a command line tool that helps you easily download, configure and manage New Relic Platform Plugins.  For more information, visit the [Getting Started with the Platform Installer section of New Relic’s Community Forum](https://discuss.newrelic.com/t/getting-started-with-the-platform-installer/842).


**NOTE:** We recommend using the New Relic Platform Installer for installing and running your Blue Medora plugins for New Relic. Issues can arise by running a plugin directly in the foreground (e.g., when the machine reboots, the process will not be started again). The NPI automates much of these processes.


Once the NPI tool has been installed, run the following command:

```
  ./npi install com.bluemedora.cisco.nexus
``` 

**Note:** This command will take care of the creation of `newrelic.json` and `plugin.json` files described in the [Configuring the Plugin](#Configuring-the-Plugin) section.

### Installing the Plugin Manually

#### Downloading and Extracting the Plugin

The latest version of the plugin can be downloaded from the locations listed in the [Obtaining the Plugin](#obtaining-the-plugin) section.  Once the plugin is on your box, extract it to your preferred directory location.

#### Configuring the Plugin

Refer to the [Configuring the Plugin](#Configuring-the-Plugin) section, for details on setting up the plugin. 

#### Running the Plugin

To run the plugin, navigate to the directory where the plugin was extracted, then execute the following command from your terminal or command line window:

```
  java -jar plugin.jar
```
 
----
    
## Configuring the Plugin
From the extracted plugin folder you receive when downloading your plugin, you will find the following files: 

```
  plugin.jar
  eula.txt
  oss_attribution.txt
  [config folder]
    newrelic.template.json
    plugin.template.json 
    plugin_license.json
```
The "template" .json files found in the config folder must be modified (i.e., customized) and renamed prior to setting up the plugin for monitoring.

### Configuring the `newrelic.template.json` file

The first file, `newrelic.template.json`, contains configurations used by all Platform plugins (e.g., license key, logging information, proxy settings) and can be shared across your plugins.
Make a copy of this template and rename it to `newrelic.json`. Listed below are the configurable fields within the `newrelic.json file`:

**New Relic License Key** - The only required field in the `newrelic.json` file is the License Key. This unique identifier informs New Relic about the specific account tied to the plugin. For more information on the License Key, [refer to the New Relic License key documentation](https://docs.newrelic.com/docs/accounts-partnerships/accounts/account-setup/license-key).

```
{
   “license_key”: “YOUR_LICENSE_KEY_HERE”
}
```

**Logging Configuration**

By default Platform plugins will have their logging turned on; however, you can modify these settings with the following configurations:

`log\_level` - The log level. Valid values: [debug, info, warn, error, fatal]. Defaults to info.

`log\_file\_name` - The log file name. Defaults to newrelic_plugin.log.

`log\_file\_path` - The log file path. Defaults to logs.

`log\_limit\_in\_kbytes` - The log file limit in kilobytes. Defaults to 25600 (25 MB). If limit is set to 0, the log file size would not be limited.

```
{
  "license_key": "YOUR_LICENSE_KEY_HERE",
  "log_level": "debug",

  "log_file_path": "log_file_path": “logs"
}
```

**Proxy Configuration**

If you are running your plugin from a machine that runs outbound traffic through a proxy, you can use the following optional configurations in your newrelic.json file:

`proxy\_host` - The proxy host (e.g. webcache.example.com)

`proxy\_port` - The proxy port (e.g. 8080). Defaults to 80 if a proxy_host is set

`proxy\_username` - The proxy username

`proxy\_password` - The proxy password


**Example:**

```
{
  "license_key": "YOUR_LICENSE_KEY_HERE",

  "proxy_host": "proxy.mycompany.com",

  "proxy_port": 9000
}
```

#### Configuring the `plugin.template.json` file: 

The second file, `plugin.template.json`, contains data specific to each plugin (e.g., a list of hosts and port combinations for what you are monitoring). Templates for both of these files should be located in the ‘config’ directory in your extracted plugin folder.

Make a copy of this template and rename it to `plugin.json`. Shown below is an example of the `plugin.json` file’s contents.

**NOTE:** You can add multiple objects to the “agents” array to monitor multiple Cisco Nexus instances. 

**NOTE:** Each object in the "agents" array should have a unique "instance_name".

**Fields**

| Field Name  |  Description |
|:------------- |:-------------|
| instance_name | The name of your New Relic Cisco Nexus instance that will appear in the User Interface |
| version | SNMP version acceptable values `v2c` or `v3` for SNMP Version 2 or Version 3 respectively |
| management_ip | IP address of Nexus Switch |
| snmp_port | Optional field, port SNMP communicates over. Defaults to `161` |
| community_string | Only applicable if `version` is `v2c`. The SNMP community string required to connect |
| security_level | Only applicable if `version` is `v3`. Acceptable values are `auth_priv`, `auth_nopriv`, or `noauth_nopriv` |
| user | Only applicable if `version` is `v3`. SNMP user name |
| auth_password | Only applicable if `version` is `v3`. SNMP Authentication password. Only needed if `security_level` is `auth_priv` or `auth_nopriv` |
| privacy_password | Only applicable if `version` is `v3`. SNMP privacy password, Only needed if `security_level` is `auth_priv` |
| privacy_type | Only applicable if `version` is `v3`. SNMP encryption type. Acceptable values are `privAES256`, `privAES192`, `privAES128`, `privDES`, or `priv3DES` for  256 bit AES, 192 bit AES, 128 bit AES, DES, or 3-DES respectively |

**Example:**

```
{
  "agents": [
    {
      "instance_name": "Your SNMP Version 2 Instance",
      "version": "v2 or v2c",
      "management_ip": "your_value_here",
      "community_string": "your_value_here",
      "snmp_port": 161
    },
    {
      "instance_name": "Your SNMP Version 3 Instance",
      "version": "v3",
      "management_ip": "your_value_here",
      "user": "your_value_here",
      "auth_password": "your_value_here",
      "privacy_password": "your_value_here",
      "authentication_type": "authSHA or authMD5",
      "privacy_type": "privAES256 or privAES192 or privAES128 or privDES or priv3DES",
      "security_level": "auth_priv or auth_nopriv or noauth_nopriv"
    }
  ]
}
```

## Using the Plugin
For more information about navigating New Relic’s user interface, refer to their [Using a plugin documentation](https://docs.newrelic.com/docs/plugins/plugins-new-relic/using-plugins/using-plugin) section.

## Support Resources
For questions or issues regarding the Blue Medora Cisco Nexus Plugin for New Relic, visit http://support.bluemedora.com. 

## Metrics Source Documentation

**Switch Overview**

| Metric Name  |  Description |
|:------------- |:-------------|
| Total Throughput (MB/sec) | The total throughput in megabytes per second   |
| Total Packets (Packets/sec)  | The total amount of packets per second |
| Port Status Count  | The number of ports per status (Down, Up, or In Error) |

**Ports**

| Metric Name  |  Description |
|:------------- |:-------------|
| Inbound Throughput (MB/sec) | The inbound throughput of a port in megabytes per second  |
| Outbound Throughput (MB/sec)  | The outbound throughput of a port in megabytes per second |
| Inbound Packets (Packets/sec)  | The amount of inbound packets per second of a port  |
| Outbound Packets (Packets/sec) | The amount of outbound packets per second of a port |

**Summary**

| Metric Name  |  Description |
|:------------- |:-------------|
| Inbound (bytes/sec) | The total inbound throughput in bytes per second |
| Outbound (bytes/sec)  | The total outbound throughput in bytes per second |
| Ports in Error (%)  | Percentage of ports in which the status is "In Error" |
| Ports Up (%) | Percentage of ports in which the status is "Up" |
| Ports Down (%) | Percentage of ports in which the status is "Down" |

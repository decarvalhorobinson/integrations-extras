# Agent Check: OctoPrint

## Overview

This check monitors [OctoPrint][1] through the Datadog Agent.

## Setup

Follow the instructions below to install and configure this check for an Agent running on a host. For containerized environments, see the [Autodiscovery Integration Templates][2] for guidance on applying these instructions.

### Installation

To install the OctoPrint check on your host:


1. Install the [developer toolkit][3] on any machine.

2. Run `ddev release build octoprint` to build the package.

3. [Download the Datadog Agent][4].

4. Upload the build artifact to any host with an Agent and
 run `datadog-agent integration install -w
 path/to/octoprint/dist/datadog_octoprint*.whl`.

### Configuration

1. Edit the `octoprint.d/conf.yaml` file, in the `conf.d/` folder at the root of your Agent's configuration directory to start collecting OctoPrint performance data. See the [sample octoprint.d/conf.yaml][5] for all available configuration options.

2. [Restart the Agent][6].

### Validation

[Run the Agent's status subcommand][7] and look for `octoprint` under the Checks section.

### Logs

By default this integration assumes that you are using the [OctoPi][10] image that is pre-configured to run OctoPrint from a Raspberry Pi.

The logs that it collects by default (and their default locations) are:

- OctoPrint App log: `/home/pi/.octoprint/logs`
- OctoPrint Webcam log: `/var/log/webcamd.log`
- HA Proxy log: `/var/log/haproxy.log`

Any or all of these may be changed or removed by modifying the integration's `conf.yaml` file.

#### Log Processing

OctoPrint uses its own log format (not an object format), so making better use of the logs requires creation of a log processing pipeline with some parsing rules. For example:

1. Main Pipeline: "OctoPrint"
    1. Sub Pipeline 1: "OctoPrint Print Job"
        1. Grok parser rule:
            - `OctoPrint_Print_Job %{date("yyyy-MM-dd HH:mm:ss,SSS"):date}\s+-\s+%{notSpace:source}\s+-\s+%{word:level}\s+-\s+Print\s+job\s+%{notSpace:job_status}\s+-\s+%{data::keyvalue(":"," ,")}`
    1. Sub Pipeline 2: "General OctoPrint Log"
        1. Grok parser rule:
            - `General_OctoPrint_Log %{date("yyyy-MM-dd HH:mm:ss,SSS"):date}\s+-\s+%{notSpace:source}\s+-\s+%{word:level}\s+-\s+%{data:message}`

For more information, see the [Datadog Log Processing documentation][11].

## Data Collected

### Metrics

See [metadata.csv][8] for a list of metrics provided by this integration.

### Service Checks

OctoPrint does not include any service checks.

### Events

OctoPrint does not include any events.

## Troubleshooting

Need help? Contact [Datadog support][9].

[1]: https://octoprint.org/
[2]: https://docs.datadoghq.com/agent/kubernetes/integrations/
[3]: https://docs.datadoghq.com/developers/integrations/new_check_howto/#developer-toolkit
[4]: https://app.datadoghq.com/account/settings#agent
[5]: https://github.com/DataDog/integrations-extras/blob/master/octoprint/datadog_checks/octoprint/data/conf.yaml.example
[6]: https://docs.datadoghq.com/agent/guide/agent-commands/#start-stop-and-restart-the-agent
[7]: https://docs.datadoghq.com/agent/guide/agent-commands/#agent-status-and-information
[8]: https://github.com/DataDog/integrations-extras/blob/master/octoprint/metadata.csv
[9]: https://docs.datadoghq.com/help/
[10]: https://octoprint.org/download/
[11]: https://docs.datadoghq.com/logs/processing/

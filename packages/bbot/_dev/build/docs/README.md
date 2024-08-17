# BBOT integration

Please read this page in its entirety as this integration requires some setup.

This integration is for [BBOT](https://www.blacklanternsecurity.com/bbot/), an Attack Surface Management (ASM) Open Source Inteligence (OSINT) Tool. BBOT itself stands for Bighuge BLS OSINT Tool (BBOT).

This integration requires the external use of BBOT. You will have to download and run the tool apart from this integration. Once your scan is complete, this integration will ingest the results into Elastic.

As of version `0.3.0` of this integration only BBOT `v2.x` is supported. All examples provided by this module now assume use of BBOT `v2.x`.

If you have existing BBOT `v1.x` data in Elasticsearch you should archive this data prior to migration to use and ingest of BBOT `v2.x` scan data, as there may be fundamental type conflicts between fields.

This tool is used to enhance your external knowledge of your environment. This is done through the integration of many tools into BBOT providing a overview of your attack surface. Here is [how it works](https://www.blacklanternsecurity.com/bbot/how_it_works/).

**Important Note**

You will have to provide the following parameter in your BBOT scan for your output.json to be formatted correctly.

`-c modules.json.siem_friendly=true`

Or if using the HTTP output modules,

`-c modules.http.siem_friendly=true`


**Example BBOT Scan**

If using the integration to collect log files from disk, you can simply use the json output module.

`bbot -t example.com -p subdomain-enum -c modules.json.siem_friendly=true -om json`


If using the integration to receive events via HTTP endpoint listener, you can use the the http output module.

`bbot -t example.com -p subdomain-enum --config modules.http.url=http://your.elastic.agent:8080/bbot/asm_intel modules.http.username=bbot modules.http.password=P@55w0rd modules.http.siem_friendly=true -om http`

You will have to configure the path for the output file within the integration settings. A common and popular path that could work here is:

The output modules can also be configured as part of the bbot.yml or preset files.

```
config:
  modules:
    http:
      url: 'https://your.elastic.agent:8443'
      method: POST
      username: 'bbot'
      password: 'P@55w0rd'
      siem_friendly: true
    json:
      siem_friendly: false
```

**Example BBOT Path**

`/home/<user>/.bbot/scans/*/output.json`

BBOT Scanning [Documentation](https://www.blacklanternsecurity.com/bbot/scanning/).

- `bbot` dataset: Made up of the findings found in the BBOT Scans.

## Logs

### ASM Findings

{{event "asm_intel"}}

{{fields "asm_intel"}}

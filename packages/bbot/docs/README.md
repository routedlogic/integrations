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

An example event for `asm_intel` looks as following:

```json
{
    "@timestamp": "2024-02-29T01:41:47.779Z",
    "agent": {
        "ephemeral_id": "61be6f83-c99a-4e6e-b908-4907ac91cc26",
        "id": "124f26ba-51e7-45bf-948e-e774cdc6ec99",
        "name": "elastic-agent-91731",
        "type": "filebeat",
        "version": "8.14.3"
    },
    "bbot": {
        "id": "DNS_NAME:f57ba0828becd7bf94faa616db081ed06f31bd3d",
        "module": "TARGET",
        "module_sequence": "TARGET",
        "scan": "SCAN:725368977d3a680e579707504e59428a7e3acc9d",
        "scope_distance": 0,
        "source": "SCAN:725368977d3a680e579707504e59428a7e3acc9d",
        "tags": [
            "resolved",
            "a-record",
            "target",
            "in-scope",
            "subdomain"
        ],
        "type": "DNS_NAME"
    },
    "data_stream": {
        "dataset": "bbot.asm_intel",
        "namespace": "44524",
        "type": "logs"
    },
    "ecs": {
        "version": "8.11.0"
    },
    "elastic_agent": {
        "id": "124f26ba-51e7-45bf-948e-e774cdc6ec99",
        "snapshot": false,
        "version": "8.14.3"
    },
    "event": {
        "agent_id_status": "verified",
        "dataset": "bbot.asm_intel",
        "ingested": "2024-08-17T03:36:31Z",
        "kind": "asset",
        "original": "{\"data\":{\"DNS_NAME\":\"example.com\"},\"id\":\"DNS_NAME:f57ba0828becd7bf94faa616db081ed06f31bd3d\",\"module\":\"TARGET\",\"module_sequence\":\"TARGET\",\"resolved_hosts\":[\"123.123.123.123\"],\"scan\":\"SCAN:725368977d3a680e579707504e59428a7e3acc9d\",\"scope_distance\":0,\"source\":\"SCAN:725368977d3a680e579707504e59428a7e3acc9d\",\"tags\":[\"resolved\",\"a-record\",\"target\",\"in-scope\",\"subdomain\"],\"timestamp\":1709170907.779394,\"type\":\"DNS_NAME\"}"
    },
    "host": {
        "name": "example.com"
    },
    "input": {
        "type": "http_endpoint"
    },
    "related": {
        "hosts": [
            "123.123.123.123"
        ]
    },
    "tags": [
        "preserve_original_event",
        "forwarded",
        "bbot"
    ],
    "url": {
        "domain": [
            "example.com"
        ]
    }
}
```

**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Event timestamp. | date |
| bbot.data.asn.asn |  | keyword |
| bbot.data.asn.country |  | keyword |
| bbot.data.asn.description |  | keyword |
| bbot.data.asn.name |  | keyword |
| bbot.data.asn.subnet |  | keyword |
| bbot.data.azure_tenant.domains |  | keyword |
| bbot.data.azure_tenant.tenant-id |  | keyword |
| bbot.data.azure_tenant.tenant-names |  | keyword |
| bbot.data.code_repository.url |  | keyword |
| bbot.data.dns_name |  | keyword |
| bbot.data.dns_name_unresolved |  | keyword |
| bbot.data.email_address |  | keyword |
| bbot.data.finding.description |  | text |
| bbot.data.finding.host |  | keyword |
| bbot.data.finding.url |  | keyword |
| bbot.data.http_response.a |  | keyword |
| bbot.data.http_response.body |  | text |
| bbot.data.http_response.cname |  | keyword |
| bbot.data.http_response.content_length |  | unsigned_long |
| bbot.data.http_response.content_type |  | keyword |
| bbot.data.http_response.csp.domains |  | keyword |
| bbot.data.http_response.failed |  | boolean |
| bbot.data.http_response.hash.\* |  | keyword |
| bbot.data.http_response.header-dict.\* |  | keyword |
| bbot.data.http_response.header-dict.location |  | text |
| bbot.data.http_response.header.\* |  | keyword |
| bbot.data.http_response.host |  | keyword |
| bbot.data.http_response.input |  | keyword |
| bbot.data.http_response.lines |  | unsigned_long |
| bbot.data.http_response.location |  | keyword |
| bbot.data.http_response.method |  | keyword |
| bbot.data.http_response.path |  | keyword |
| bbot.data.http_response.port |  | keyword |
| bbot.data.http_response.raw_header |  | text |
| bbot.data.http_response.request |  | keyword |
| bbot.data.http_response.scheme |  | keyword |
| bbot.data.http_response.status_code |  | unsigned_long |
| bbot.data.http_response.time |  | keyword |
| bbot.data.http_response.timestamp |  | keyword |
| bbot.data.http_response.title |  | keyword |
| bbot.data.http_response.url |  | keyword |
| bbot.data.http_response.webserver |  | keyword |
| bbot.data.http_response.words |  | unsigned_long |
| bbot.data.ip_address |  | keyword |
| bbot.data.open_tcp_port |  | keyword |
| bbot.data.org_stub |  | keyword |
| bbot.data.protocol.host |  | keyword |
| bbot.data.protocol.port |  | unsigned_long |
| bbot.data.protocol.protocol |  | keyword |
| bbot.data.raw_dns_record.answer |  | keyword |
| bbot.data.raw_dns_record.host |  | keyword |
| bbot.data.raw_dns_record.type |  | keyword |
| bbot.data.scan.id |  | keyword |
| bbot.data.scan.name |  | keyword |
| bbot.data.scan.preset.\* |  |  |
| bbot.data.scan.preset.config.\* |  |  |
| bbot.data.scan.preset.config.\*.\* |  |  |
| bbot.data.scan.preset.config.\*.\*.\* |  |  |
| bbot.data.scan.target.blacklist_hash |  | keyword |
| bbot.data.scan.target.hash |  | keyword |
| bbot.data.scan.target.scope_hash |  | keyword |
| bbot.data.scan.target.seed_hash |  | keyword |
| bbot.data.scan.target.seeds |  | keyword |
| bbot.data.scan.target.strict_scope |  | boolean |
| bbot.data.scan.target.whitelist |  | keyword |
| bbot.data.scan.target.whitelist_hash |  | keyword |
| bbot.data.social.platform |  | keyword |
| bbot.data.social.profile_name |  | keyword |
| bbot.data.social.url |  | keyword |
| bbot.data.storage_bucket.name |  | keyword |
| bbot.data.storage_bucket.url |  | keyword |
| bbot.data.technology.host |  | keyword |
| bbot.data.technology.technology |  | keyword |
| bbot.data.technology.url |  | keyword |
| bbot.data.url |  | keyword |
| bbot.data.url_unverified |  | keyword |
| bbot.data.waf.host |  | keyword |
| bbot.data.waf.url |  | keyword |
| bbot.data.waf.waf |  | keyword |
| bbot.data.web_parameter.description |  | keyword |
| bbot.data.web_parameter.host |  | keyword |
| bbot.data.web_parameter.name |  | keyword |
| bbot.data.web_parameter.original_value |  | keyword |
| bbot.data.web_parameter.type |  | keyword |
| bbot.data.web_parameter.url |  | keyword |
| bbot.discovery_context |  | text |
| bbot.discovery_path |  |  |
| bbot.dns_children.\* |  | keyword |
| bbot.host |  | keyword |
| bbot.id |  | keyword |
| bbot.module |  | keyword |
| bbot.module_sequence |  | keyword |
| bbot.parent |  | keyword |
| bbot.resolved_hosts |  | keyword |
| bbot.scan |  | keyword |
| bbot.scope_description |  | keyword |
| bbot.scope_distance |  | integer |
| bbot.source |  | keyword |
| bbot.tags |  | keyword |
| bbot.timestamp |  | keyword |
| bbot.type |  | keyword |
| bbot.web_spider_distance |  | integer |
| data_stream.dataset | Data stream dataset. | constant_keyword |
| data_stream.namespace | Data stream namespace. | constant_keyword |
| data_stream.type | Data stream type. | constant_keyword |
| input.type | Type of Filebeat input. | keyword |
| log.offset | Log offset. | long |


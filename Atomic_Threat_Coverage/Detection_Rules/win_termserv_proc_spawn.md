| Title                | Terminal Service Process Spawn                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects a process spawned by the terminal service server process (this could be an indicator for an exploitation of CVE-2019-0708)                                                                                                                                           |
| ATT&amp;CK Tactic    |   There is no documented ATT&amp;CK Tactic for this Detection Rule.  |
| ATT&amp;CK Technique |  There is no documented ATT&amp;CK Technique for this Detection Rule.  |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              |  There is no Trigger for this technique yet.  |
| Severity Level       | high |
| False Positives      | <ul><li>Unknown</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://securingtomorrow.mcafee.com/other-blogs/mcafee-labs/rdp-stands-for-really-do-patch-understanding-the-wormable-rdp-vulnerability-cve-2019-0708/](https://securingtomorrow.mcafee.com/other-blogs/mcafee-labs/rdp-stands-for-really-do-patch-understanding-the-wormable-rdp-vulnerability-cve-2019-0708/)</li></ul>  |
| Author               | Florian Roth |
| Other Tags           | <ul><li>car.2013-07-002</li><li>car.2013-07-002</li></ul> | 

## Detection Rules

### Sigma rule

```
title: Terminal Service Process Spawn
status: experimental
description: Detects a process spawned by the terminal service server process (this could be an indicator for an exploitation of CVE-2019-0708)
references:
    - https://securingtomorrow.mcafee.com/other-blogs/mcafee-labs/rdp-stands-for-really-do-patch-understanding-the-wormable-rdp-vulnerability-cve-2019-0708/
author: Florian Roth
date: 2019/05/22
tags:
    - car.2013-07-002
logsource:
    product: windows
    category: process_creation
detection:
    selection:
        ParentCommandLine: '*\svchost.exe*termsvcs'
    filter:
        Image: '*\rdpclip.exe'
    condition: selection and not filter
falsepositives:
    - Unknown
level: high


```





### es-qs
    
```
(ParentCommandLine.keyword:*\\\\svchost.exe*termsvcs AND (NOT (Image.keyword:*\\\\rdpclip.exe)))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/Terminal-Service-Process-Spawn <<EOF\n{\n  "metadata": {\n    "title": "Terminal Service Process Spawn",\n    "description": "Detects a process spawned by the terminal service server process (this could be an indicator for an exploitation of CVE-2019-0708)",\n    "tags": [\n      "car.2013-07-002"\n    ],\n    "query": "(ParentCommandLine.keyword:*\\\\\\\\svchost.exe*termsvcs AND (NOT (Image.keyword:*\\\\\\\\rdpclip.exe)))"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(ParentCommandLine.keyword:*\\\\\\\\svchost.exe*termsvcs AND (NOT (Image.keyword:*\\\\\\\\rdpclip.exe)))",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Terminal Service Process Spawn\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(ParentCommandLine:"*\\\\svchost.exe*termsvcs" AND NOT (Image:"*\\\\rdpclip.exe"))
```


### splunk
    
```
(ParentCommandLine="*\\\\svchost.exe*termsvcs" NOT (Image="*\\\\rdpclip.exe"))
```


### logpoint
    
```
(ParentCommandLine="*\\\\svchost.exe*termsvcs"  -(Image="*\\\\rdpclip.exe"))
```


### grep
    
```
grep -P '^(?:.*(?=.*.*\\svchost\\.exe.*termsvcs)(?=.*(?!.*(?:.*(?=.*.*\\rdpclip\\.exe)))))'
```




# Incident Title

## Summary

During the initial creation of ADSync accounts we had an issue with a network policy that would expire said service accounts after a given period of time, which left us with an arbitrary amount of service accounts in the DFDS AD that did not correlate with the credentials registered with their respective parameter stores (eu-central-1). Due to the fact that we don't have a centralized vault service available in DFDS we cannot push updated credentials to Azure pipelines, without breaking existing customers CI/CD flows, which at the time was less desirable then leaving unused capabilities with invalid service account credentials in their param store. 

Over time more and more people started using our capabilities, including the service account concept. This created some challenges with our services, when synchronizing AD service accounts, due to the nature of our sync algorithm (we don't do diffsync, yet!) which would cause random services to experience "access denied" disruptions as the service account was deleted tempoarily by ADSync and re-added again with a fresh credential pair that was posted to the AWS parameter store. Considering that re-writing the synchronization strategy is not trivial, we decided that an acceptable "quick fix" was to simply service peoples via our GOTO help desk if they experienced problems. 

The problem was further compounded by two external facts:

1) SAM names in Windows are very constrained (https://docs.microsoft.com/en-us/windows/win32/adschema/a-samaccountname) 
2) AWSSDK cannot bootstrap its own client configuration via appsettings.json in .net core without a factory (https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/net-dg-config-netcore.html). Which was a breaking change we did not catch in our migration from .NET classic to .NET core.


To summarize we have fixed most of the corner cases related to synchronizing service accounts, without doing the work of re-writing the sync algo. However there is still a problem with the "name-shortener-logic" in ADSync, which due to the AD constraints (see #1), cannot be solved AS-IS. Thus if you deleted and re-create accounts you could potentially experience this issue and if that is the case please contact our GOTO help desk for further assistance.


## Timeline

All times in CET.

| Time | Event |
| :--- | --- |
| 20xx-MM-DD HH:mm | Short event description | 

## Contributing Factors

- 

## Lessons Learned


## Action Items

- 

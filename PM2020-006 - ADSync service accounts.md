# ADSync service accounts

## Summary

During the initial implementation of our ADSync service account feature we had some bugs in our code and a AD policy that would expire said service accounts after a given period of time. This left us with an arbitrary amount of service accounts in the DFDS AD that did not correlate with the credentials registered with their respective AWS parameter stores (eu-central-1). Because we don't have a centralized vault service in DFDS and teams where already using service accounts in their Azure pipelines we decided that it was simpler to just leave some service accounts in a broken state and fix them manually as people onboarded the new feature.

Over time more and more teams started using our capability conceåt, including the service account concept, we noticed another problem with the current sync algorithm (we don't do diffsync, yet!) which would cause random services to experience "access denied" disruptions as the service account was deleted temporarily by ADSync and re-added a moment later with a fresh credential pair. Considering that re-writing the synchronization strategy is not trivial, we decided that an acceptable "quick fix" was to not delete service accounts and manually clean-up service accounts if teams re-cycled their capability names.

To summarize we have fixed most of the corner cases related to synchronizing service accounts, without doing the work of re-writing the sync algo. However there is still a problem with the "name-shortener-logic" in ADSync, which due to the AD constraints (see Contributing Factors) cannot be solved AS-IS. Thus if you deleted and re-create capabilities you could potentially experience issues with your service account and if that is the case please contact our GOTO help desk for further assistance.

## Timeline

All times in CET.

| Time | Event |
| :--- | --- |
| 2020-02-24 10:50 | Identified problem with ADSync assuming roles for certain capabilities.  |
| 2020-02-24 10:50 | Identified problem with ADSync not overwriting parameters in AWS ParameterStore.  |
| 2020-03-03 10:00 | Decided to not patch existing service accounts to avoid breaking capabilities that was already using the feature.  |
| 2020-03-05 18:35 | Added userAccessControl flags to new service account to avoid expiration policy from taking affect.  |
| 2020-03-17 20:29 | Disable deletion of AD sync account.  |


## Contributing Factors

- SAM names in Windows are very constrained which leads to a loss of data when our "name-shortener-logic" converts a CapabilityIdentifiers to a SAM compatible string. (https://docs.microsoft.com/en-us/windows/win32/adschema/a-samaccountname)

- AWS SDK cannot bootstrap its own client configuration via appsettings.json in .net core without a factory (https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/net-dg-config-netcore.html). This was a breaking change we did not catch in our migration from .NET classic to .NET core.


## Lessons Learned

- Test migrations from .NET Classic to .NET Core more aggresively. Just because it compiles and you didn't change the code, does not mean it works.
 

## Action Items

- DevEX will update all service accounts in the near future by synchronizing all passwords from the AWS ParameterStore to our on-prem AD. We will post a maintenance notice ahead-of-time and reach out to people whose pipelines are affected by the patch to ensure you are not unprepared for this event.

- DevEx has placed an action item on our development roadmap to implement a new ADSync provider in the near future. This will have the added benefit of event enabling the ADSync service which would allow teams to listen in to various events that can affect their service accounts.

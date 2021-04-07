# Incident Title

ADFS Outage

## Summary

On Wednesday 31st March, the token signing and token decryption certificates applied to ADFS expired. This caused 
problems signing in using SAML and logging in via the AWS web console for platform users.

## Timeline

All times in UTC.

| Time | Event |
| :--- | --- |
| 2021-03-31 12:11 | Reports of issues signing into the AWS web console via ADFS | 
| 2021-03-31 12:21 | Issue logging in verified | 
| 2021-03-31 12:25 | Datacenters team contacted reporting the issue | 
| 2021-03-31 12:49 | Member of datacenters team joined call to begin investigating the issue | 
| 2021-03-31 13:02 | Identified expired token signing and decryption certificates | 
| 2021-03-31 13:18 | Certificate rolled over and login via ADFS confirmed working via root account manual fix of federation metadata. Ran script to update all account federation metadata | 
| 2021-03-31 13:30 | Script completed to update account federation metadata. Confirmed working | 

## Contributing Factors

- We depend on datacenters team to manage ADFS
- No monitoring or record of certificate expiry
- Auto renewal feature not configured in ADFS
- Dependency on ADFS for SSO

## Lessons Learned

- Infrastructure are not prioritising/monitoring their ADFS implementation
- We are not monitoring the ADFS implementation

## Action Items

- Implement monitoring of ADFS (particularly certificate expiry via script or even calendar)
- Identify a clear path for migration away from ADFS to alternative solution

## Atendees

- RF, PW, RR, AN, EC

## Stats

- Category: Authentication/Authorisation
- Time to detection: ???
- Time to recovery: ???

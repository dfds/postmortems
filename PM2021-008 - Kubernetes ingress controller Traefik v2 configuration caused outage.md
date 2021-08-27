# Kubernetes ingress controller Traefik v2 configuration caused outage

## Summary

As part of the migration away from Kubernetes ingress controller Traefik v1, we changed the main Kubernetes ingress controller to v2 of Traefik. Due to misconfiguration which didn't allow insecure headers ("X-FORWARDED-*"), to be forwarded causing at least the Okta authentication workflow to go down. This meant that all services using Okta got affected. Changes were rolled back and started working again.

## Timeline

All times in UTC.

| Time             | Event                                                                                             |
| :--------------- | ------------------------------------------------------------------------------------------------- |
| 2021-08-26 08:22 | AWS Load Balancers were updated to route requests to the new Traefik v2 reverse proxy.            |
| 2021-08-26 08:44 | Functional tests indicates all fine, and this is reported over Slack.                             |
| 2021-08-26 09:26 | Developer reports high count of HTTP Status code 307 (redirects), in particular the Okta service. |
| 2021-08-26 09:26 | Investigations started in Cloud Engineering + Developer                                           |
| 2021-08-26 09:32 | We are informed that the the online booking system is down.                                       |
| 2021-08-26 09:48 | Due to criticality rollback takes priority over investigation. Rollback performed.                |
| 2021-08-26 09:49 | Service operational.                                                                              |
| 2021-08-26 10:00 | Deep dive investigation started together with Okta team, and root cause was found later.          |

## Contributing Factors

- Request headers were checked prior to migration, but that x-forwared-proto changed from https to http was overlooked.
- Unreasonable long detection time caused complete loss of online bookings for a bit over 1 hour.
- All teams are always responsible for monitoring their application after all critical changes.
- Some announcements on Slack are sometimes drowning in other noise.
- Could a diff between requests headers made us aware before the migration?

## Action Items

- Reflect on how our critical announcements can become more visible.
- Reflect if the ALBs we have tested with should have been communicated to the teams, so they could have tested their scenarios too. In this incident however it could have been difficult since the down service has Cloudfront in front of the ALB.

## Lessons Learned

- Not all scenarios can be tested by Cloud Engineering. We have tested some of our own applications for weeks.

## Attendees

- EC, MM, WC, AN

## Stats

- Category: Kubernetes
- Time to detection: 64 minutes
- Time to recovery:  23 minutes

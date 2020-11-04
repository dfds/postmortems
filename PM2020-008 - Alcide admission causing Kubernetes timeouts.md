# Alcide admission causing Kubernetes timeouts

## Summary
While having an Alcide workshop an admission controller was applied with the understanding that it wouldn't be disruptive. But it unfortunately was, causing all create and update operations on mainly deployments (but also apiGroups: ["", "apps", "batch", "extensions"]) to timeout. Suspecting the admission controller to being slow at responding.

## Timeline

All times in CET.

| Time | Event |
| :--- | --- |
| 2020-11-04 13:00 | Alcide workshop started |
| 2020-11-04 13:13 | Applied disruptive Alcide admission controller |
| 2020-11-04 14:41 | Users reporting timeout issues when deploying to Hellman (Kubernetes cluster) |
| 2020-11-04 15:04 | Verification of the timeout issue |
| 2020-11-04 15:15 | Toggling disruptive Alcide admission controller to verify fix |
| 2020-11-04 15:39 | Permanently deleted disruptive Alcide admission controller |

## Contributing Factors

- Doing a workshop using production as sandbox
- Smoketesting not in place to catch breaking capabilities
- Knowledge of Alcide wasn't distributed among team members because of pending deprecation
- False safety with "re-applying" something that had already been there

## Lessons Learned

- New stuff is applied in sandbox clusters
- Acquire Alcide knowledge required to maintain component until fully deprecated

## Action Items

- Monitor Kubernetes deployments for timeouts
- Monitor 5xx status codes for Kube API server

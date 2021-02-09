# Inotify file watches exhaustion

## Summary

Inotify allow processes to register themselves to be notified when certain filesystem events happen. There's a configurable system limit to how many files you can monitor. On our Kubernetes nodes, this limit is currently set to 100.000.

When the system exhausts the available file watches, any attempts to add watches will fail with the error message "no space left on device". One of the effects is that the node will fail to reliably ship logs out.

On Monday 20. January one of our nodes (`ip-10-0-32-54.eu-west-1.compute.internal`) ran out of available file watches. The primary culprit was identified and terminated, and team was notified about the issue.

## Timeline

All times in CET.

| Time             | Event                                           |
| :--------------- | ----------------------------------------------- |
| 2020-01-20 13:50 | Node starts reporting "no space left on device" |
| 2020-01-22 10:39 | Pod killed, releasing file watches              |

## Contributing Factors

- Misbehaving code running in a capability container

## Lessons Learned

We should look into monitoring the current file watches usage, and configure alert thresholds based on the currently configured system limit.

If this turns out to be a recurring issue, we may want to spent some time to research any commonality in code that causes this. Any learnings from this could be incorporated in our guidelines and/or workshops.

## Action Items

- Setup monitoring and alerting for file watches usage

# Stats

- Category: Self-service
- Time to detection: ?
- Time to recovery: ?

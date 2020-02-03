# ADSync migration issues

## Summary

The initial analysis for migrating ADSync from .NET classic to .NET core failed to predict a number of issues with third party components (TopShelf/Serilog/Cake) and our legacy pipeline could not handle the conventions for variable substitution in MS v. 2.0 DevOps tasks. Furthermore OAuth authorization was more or less disabled by changes to the capability-services ingress controller added in this commit @ https://github.com/dfds/capability-service/commit/dbda32b5d823ab9c220b3fbc906b73decaab22dc#diff-ac6b62a73dd8a93cecbe56751092c43e, which caused the self-service solution as a whole to not work as expected because capability-service was no longer able to process oAuth tokens. This specific problem further highlights the importance of being able to debug a full k8s setup on local developer machines.


## Timeline

All times in CET.

| Time | Event |
| --- | --- |
| 20202-01-16 12:00 | Cake was not copying appsettings.json, did not create a windows exe and had problems merging in variable with complex names (e.g. my.variable). These issues ultimately forced us to setup new CI/CD pipelines (ADSync-CI-2/ADSync-CD-2). | 
| 20202-01-17 12:00 | Problem with merging complex pipeline variables was fixed by re-working the pipeline steps. | 
| 20202-01-20 12:00 | Issues with .NET Core Windows Service in v. 2.2 which caused base dir to be "System32" was identified. This was not caught by our unit/integration tests (as they run the code in console mode), which might indicate a need for more e2e tests in ADSync to ensure this problem does not re-emerge. |
| 20202-01-24 12:00 | Problem with Serilog file writes in .NET Core Windows Services was identified. We fixed it by moving the entire configuration to a file based approach and providing a specific path to the log folder. This has the added value of enabling us to log to another destination then the application folder |
| 20202-01-24 12:00 | Identified issue with changes to the ingress controller manifest in the capability-service which caused Traefik to override the OAuth bearer token with a basic alternative that cannot be read by the OAuth middleware. |
 

## Contributing Factors

- We opted to refactor the existing code, rather then just trashing the old solution and migrating the ADSync code we really need (which makes up less then 15% of the overall codebase in the legacy solution)
- MS Migration tool did not detect any of these issues.
- TopShelf does not solve the "base dir" issue in their wrapper, which makes it questionable if we should continue with this package moving forward as it adds more code/complexity then the native alternatives.
- SeriLog should throw a exception when it cannot create a log file. This doesn't happen and the error message is highly misleading. This all means we can only reliably detect this type of problem by looking in the Windows log @ the server-level.


## Lessons Learned

- When working with windows services it is absolutely critical that we have more e2e tests as the unit/integration tests will run the code as a "console", which behaves differently.
- Prioritize consoles + cron jobs over windows services to avoid having to compile to specific targets (our code should work on all OS's, all the time).
- Don't mix yml-based pipelines with GUI-based pipeline. Tasks will not behave identically in all cases, which makes it hard to predict the outcome of a "hybrid-pipeline".
- Don't have mixed responsibilities between IaC components and application code (e.g. Traefik forces everyone to use basic, but API controller will only accept OAuth. This will always cause a 401!)

## Action Items

- We should have a talk with Rune/Platform about what responsibilities our ingress controllers should have in general and mock up some guidelines for CAG. 
- We need to streamline our pipelines so they become more generic and less application centric.
- All applications should run in containers. Configuring your application hosting environment via inline powershell code or referencing scripts in your repo to setup build agents is bound to fail.
- We need to refactor NUnit -> XUnit test so we can use the CLI-based test runner and don't have to rely on the VSTest job as it binds us to windows agents.
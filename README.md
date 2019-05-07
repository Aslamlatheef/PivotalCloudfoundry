# PivotalCloudfoundry Cook Book and Running notes

Cloud Foundry is a platform for running applications and one-off tasks. The essence of Cloud Foundry is to provide companies with the speed, simplicity, and control they need to develop and deploy applications.

1. Provisioning VMs, OSs, middleware, and databases
2. Application runtime configuration and memory tuning
3. User management and SSO integration
4. Load balancing and traffic routing
5. Centralized log aggregation
6. Scaling
7. Security auditing
8. Providing fault tolerance and resilience
9. Service discovery
10. Application placement and container creation and orchestration
11. Blue/green deployments with the use of canaries


### Cloud Operating System
Cloud Foundry is effectively a cloud-based operating system that utilizes cloud-based resources, which are hidden and abstracted away from the end user

in the same way that the OS on your phone, tablet, or laptop abstracts the underlying physical compute resource, Cloud Foundry abstracts the infrastructure’s compute resource (specifically virtual storage, networking, RAM, and CPU). The net effect is that Cloud Foundry serves both as a standard and efficient way to deploy applications and services across different cloud-computing environments. Conversely, if you are stuck with directly using IaaS–specific APIs, it requires knowledge of the developer patterns and operations specific to the underlying IaaS technology, frequently resulting in applications becoming tightly coupled to the underlying infrastructure.

![](https://github.com/Aslamlatheef/PivotalCloudfoundry/blob/master/PCF/cloudos.png)

### The Application as the Unit of Deployment
Traditionally, deploying application code required provisioning and deploying VMs, OSs, and middleware to create a development environment for the application to run in. After that environment was provisioned, it required patching and ongoing maintenance. New environments were then created as the application moved through the deployment pipeline.

Cloud Foundry differs from traditional provisioning and orchestration engines in a fundamental way: it uses middleware and infrastructure directly, allowing streamlined development through self-service environments. Developers can build, deploy, run, and scale applications on Cloud Foundry without having to be mindful of the specific underlying infrastructure, middleware, and container implementation

Cloud Foundry allows the unit of deployment, i.e., what you deploy to run your application, to be isolated to just the application itself

it is enough to know that buildpacks provide the framework and runtime support for your applications. A specific buildpack is used to package your application with all of its dependencies. The resulting staged application is referred to as a **droplet**

**UNITS OF DEPLOYMENT** The phrase “the application is the unit of deployment” is used liberally

#### Using cf push Command to Deploy
You use the cf push command to deploy your application. It has demonstrably improved the deployment experience. From the time you run cf push to the point when the application is available, Cloud Foundry performs the following tasks:
Uploads and stores application files
- Examines and stores application metadata
- Stages the application by using a buildpack to create a droplet
- Selects an appropriate execution environment in which to run the droplet
- Starts the AI and streams logs to the Loggregator

***below*** is complete reference of pivotal * *cfpush* * commands that will help developers
![](https://github.com/Aslamlatheef/PivotalCloudfoundry/blob/master/PCF/CFPUSHCR.png)


#### Staging
Although it is part of the cf push workflow, staging is a core Cloud Foundry concept. Cloud Foundry allows users to deploy a prebuilt Docker image or an application artifact (source code or binaries) that has not yet been containerized. When deploying an application artifact, Cloud Foundry will stage the application on a machine or **VM** known as a **Cell**, using everything required to compile and run the apps locally, including the following:
- The OS stack on which the application runs
- A buildpack containing all languages, libraries, dependencies, and runtime services the app uses

The staging process results in a ***droplet*** that the ***Cell*** can unpack, compile, and run. You can then run the resulting droplet (as in the case of a Docker image) repeatedly over several Cells. The same droplet runs the same app instances over multiple Cells without incurring the cost of staging every time a new instance is run. 

    - This ability provides deployment speed and confidence that all running instances from the same droplet are identical.

### Self-Service Application Life Cycle

In most traditional scenarios, the application developer and application operator typically perform the following:
1. Develop an application
2. Deploy application services
3. Deploy an application and connect (bind) it to application services
4. Scale an application, both up and down
5. Monitor an application
6. Upgrade an application

Adopting a self-service approach removes hand-offs and potentially lengthy delays between teams. For example, the ability to deploy an application, provision and bind applications to services, scale, monitor, and upgrade are all offered by a simple call to the platform.

With Cloud Foundry, as mentioned earlier, the application or task itself becomes the single unit of deployment. Developers just push their applications to Cloud Foundry, and those applications run. If developers require multiple instances of an application to be running they can use cf scale to scale the application to N number of AIs. Cloud Foundry removes the cost and complexity of configuring infrastructure and middleware per application. Using a self-service model, users can do the following:

- Deploy applications
- Provision and bind additional services, such as messaging engines, caching solutions, and databases
- Scale applications
- Monitor application health and performance
- Update applications
- Delete applications

 Note **Deploying and scaling applications are completely independent operations** This provides the flexibility to scale at will, without the cost of having to redeploy the application every time. Users can simply scale an application with a self-service call to the platform. Through commercial products such as Pivotal Cloud Foundry, you can set up autoscaling policies for dynamic scaling of applications when they meet certain configurable thresholds.
Removing the infrastructure, OS, and middleware configuration concerns from developers allows them to focus all their effort on the application instead of deploying and configuring supporting technologies. This keeps the development focus where it needs to be

### how can you onboard application on PCF, what are the princepals that we need to follow ###

An architectural style known as cloud-native applications has been established to describe the design of applications specifically written to run in a cloud environment. These applications avoid some of the antipatterns that were established in the client-server era, such as writing data to the local filesystem. Those antipatterns do not work as well in a cloud environment because, for example, local storage is ephemeral given that VMs can move between different hosts. [The Twelve-Factor App](http://12factor.net/) explains the 12 principles underpinning cloud-native applications.

you can read about 12 principals to make application as a cloud agnostic, else you can do it **my way** 

    1. Understand the application
    2. Split Front end back end services with "Glue code."
    3. Design your data model supporting independent services
    4. isolate most of the service which can be separated and independent ( applies to both front end and back end)
    5. start creating each service 
    6. refactor the application
    
### Release Engineering through BOSH ###
the platform provides responsive IT operations, with full visibility and control over the application life cycle, provisioning, deployment, upgrades, and security patches. Several other operational benefits exist, such as built-in resilience, security, centralized user management, and better insights through capabilities like aggregated metrics and logging.

The Cloud Foundry repository is structured for use with BOSH. BOSH is an open source tool chain for release-engineering, deployment, and life cycle management. Using a YAML (YAML Ain’t Markup Language) deployment manifest, BOSH creates and deploys (virtual) machines1 on top of the targeted computing infrastructure and then deploys and runs software (in our case Cloud Foundry and supporting services) on to those created machines. Many of the benefits to operators are provided through using BOSH to deploy and manage Cloud Foundry. BOSH is often overlooked as just another component of Cloud Foundry, but it is the bedrock of Cloud Foundry and a vital piece of the ecosystem. It performs monitoring, failure recovery, and software updates with zero-to-minimal downtime

BOSH is designed to be a single tool covering the entire set of requirements of release engineering. BOSH enables software deployments to be:
1. Automated
2. Reproducible
3. Scalable
4. Monitored with self-healing failure recovery
5. Updatable with zero-to-minimal downtime

**BOSH** configures infrastructure through code. By design, BOSH tries to abstract away the differences between infrastructure platforms (IaaS or physical servers) into a generalized, cross-platform description of your deployment. This provides the benefit of being infrastructure agnostic (as far as possible).

### Built-In Resilience and Fault Tolerance ###
Cloud Foundry automates the recovery of failed applications, components, and processes. This self-healingremoves the recovery burden from the operator, ensuring speed of recovery. Cloud Foundry, underpinned by BOSH, achieves resiliency and self-healing through:
1. Restarting failed system processes
2. Recreating missing or unresponsive VMs
3. Deployment of new AIs if an application crashes or becomes unresponsive
4. Application striping across availability zones (AZs) to enforce separation of the underlying infrastructure
5. Dynamic routing and load balancing

Cloud Foundry deals with application orchestration and placement focused on even distribution across the infrastructure. The user should not need to worry about how the underlying infrastructure runs the application beyond having equal distribution across different resources (known as availability zones). The fact that multiple copies of the application are running with built-in resiliency is what matters.

Cloud Foundry provides dynamic load balancing. Application consumers use a route to access an application; each route is directly bound to one or more applications in Cloud Foundry. When running multiple instances, it balances the load across the instances, dynamically updating its routing table. Dead application routes are automatically pruned from the routing table, with new routes added when they become available.

***Note*** : Without these capabilities, the operations team is required to continually monitor and respond to pager alerts from failed apps and invalid routes. By replacing manual interaction with automated, self-healing software, applications and system components are restored quickly with less risk and downtime. The resiliency concern is satisfied once, for all applications running on the platform, as opposed to developing customized monitoring and restart scripts per application. The platform removes the ongoing cost and associated maintenance of bespoke resiliency solutions.

***Self-Healing Processes***
Traditional infrastructure as code tools do not check whether provisioned services are up and running. BOSH has strong opinions on how to create your release, forcing you to create a monitor script for the process. If a BOSH-deployed component has a process that dies, the monitor script will try to restart it.
***Self-Healing VMs***
BOSH has a Health Monitor and Resurrector. The Health Monitor uses status and life cycle events to monitor the health of VMs. If the Health Monitor detects a problem with a VM, it can trigger an alert and invoke the Resurrector. The Resurrector automatically recreates VMs identified by the Health Monitor as missing or unresponsive.

***Self-Healing Application Instance Count***
Cloud Foundry runs the application transparently, taking care of the application life cycle. If an AI dies for any reason (e.g., because of a bug in the application) or a VM dies, Cloud Foundry can self-heal by restarting new instances so as to keep the desired capacity to run AIs. It achieves this by monitoring how many instances of each application are running. The Cell manages its AIs, tracks started instances, and broadcasts state messages. When Cloud Foundry detects a discrepancy between the actual number of running instances versus the desired number of available AIs, it takes corrective action and initiates the deployment of new AIs. To ensure resiliency and fault tolerance, you should run multiple AIs for a single application. The AIs will be distributed across multiple Cells for resiliency.

***Resiliency Through Availability Zones***
Finally, Cloud Foundry supports the use of availability zones (AZs)
![](https://github.com/Aslamlatheef/PivotalCloudfoundry/blob/master/PCF/cfazones.png)
When you deploy multiple AIs, Cloud Foundry will distribute them evenly across the AZs. If, for example, a rack of servers fails and brings down an entire AZ, the AIs will still be up and serving traffic in the remaining AZs

### Aggregated Streaming of Logs and Metrics
Cloud Foundry provides insight into both the application and the underlying platform through aggregated logging and metrics. The logging system within Cloud Foundry is known as the Loggregator. It is the inner voice of the system, telling the operator and developer what is happening. It is used to manage the performance, health, and scale of running applications and the platform itself, via the following:
1. Logs provide visibility into behavior; for example, application logs can be used to trace through a specific call stack.
2. Metrics provide visibility into health; for example, container metrics can include memory, CPU, and disk-per-app instance.

below images illustrates how application logs and syslogs are separated as streams, in part to provide isolation and security between the two independent concerns, and in part due to consumer preferences. Generally speaking, app developers do not want to wade through component logs to resolve an app-specific issue. Developers can trace the log flow from the frontend router to the application code from a single log file.

![](https://github.com/Aslamlatheef/PivotalCloudfoundry/blob/master/PCF/logs.png)

In addition to logs, metrics are gathered and streamed from system components. Operators can use metrics information to monitor an instance of Cloud Foundry. Furthermore, Cloud Foundry events show specific events such as when an application is started or stopped. The benefits of aggregated log, metric, and event streaming include the following:
- You can stream logs to a single endpoint.
- Streamed logs provide timestamped outputs per application.
- Both application logs and system-component logs are aggregated, simplifying their consumption.
- Metrics are gathered and streamed from system components.
- Operators can use metrics information to monitor an instance of Cloud Foundry.
- You can view logs from the command line or drain them into a log management service such as an ELK stack (Elasticsearch, Logstash, and Kibana), Splunk, or PCF Metrics.
- Viewing events is useful when debugging problems. For example, it is useful to be able to correlate an app instance event (like an app crash) to the container’s specific metrics (high memory prior to crash).
The cost of implementing an aggregated log and metrics-streaming solution involves bespoke engineering to orchestrate and aggregate the streaming of both syslog and application logs from every component within a distributed system into a central server. Using a platform removes the ongoing cost and associated maintenance of bespoke logging solutions.


## Security
For enterprises working with cloud-based infrastructure, security is a top concern. Usually the security teams have the strongest initial objections to Cloud Foundry because it works in a way that is generally unprecedented to established enterprise security teams. However, as soon as these teams understand the strength of Cloud Foundry’s security posture, my experience is that they become one of your strongest champions.

Please refer [Security.MD](https://github.com/Aslamlatheef/PivotalCloudfoundry/blob/master/security.md#security)


## Command Reference 
---------------------------------------------------------------------------------------------------------------------------------
Available commands:
  add-blob               Add blob                                           https://bosh.io/docs/cli-v2#add-blob
  alias-env              Alias environment to save URL and CA certificate   https://bosh.io/docs/cli-v2#alias-env
  attach-disk            Attaches disk to an instance                       https://bosh.io/docs/cli-v2#attach-disk
  blobs                  List blobs                                         https://bosh.io/docs/cli-v2#blobs
  cancel-task            Cancel task at its next checkpoint                 https://bosh.io/docs/cli-v2#cancel-task (aliases: ct)
  clean-up               Clean up releases, stemcells, disks, etc.          https://bosh.io/docs/cli-v2#clean-up
  cloud-check            Cloud consistency check and interactive repair     https://bosh.io/docs/cli-v2#cloud-check (aliases: cck, cloudcheck)
  cloud-config           Show current cloud config                          https://bosh.io/docs/cli-v2#cloud-config (aliases: cc)
  config                 Show current config for either ID or both type and name https://bosh.io/docs/cli-v2#config (aliases: c)
  configs                List configs                                       https://bosh.io/docs/cli-v2#configs (aliases: cs)
  cpi-config             Show current CPI config                            https://bosh.io/docs/cli-v2#cpi-config
  create-env             Create or update BOSH environment                  https://bosh.io/docs/cli-v2#create-env
  create-release         Create release                                     https://bosh.io/docs/cli-v2#create-release (aliases: cr)
  delete-config          Delete config                                      https://bosh.io/docs/cli-v2#delete-config (aliases: dc)
  delete-deployment      Delete deployment                                  https://bosh.io/docs/cli-v2#delete-deployment (aliases: deld)
  delete-disk            Delete disk                                        https://bosh.io/docs/cli-v2#delete-disk
  delete-env             Delete BOSH environment                            https://bosh.io/docs/cli-v2#delete-env
  delete-release         Delete release                                     https://bosh.io/docs/cli-v2#delete-release (aliases: delr)
  delete-snapshot        Delete snapshot                                    https://bosh.io/docs/cli-v2#delete-snapshot
  delete-snapshots       Delete all snapshots in a deployment               https://bosh.io/docs/cli-v2#delete-snapshots
  delete-stemcell        Delete stemcell                                    https://bosh.io/docs/cli-v2#delete-stemcell (aliases: dels)
  delete-vm              Delete VM                                          https://bosh.io/docs/cli-v2#delete-vm
  deploy                 Update deployment                                  https://bosh.io/docs/cli-v2#deploy (aliases: d)
  deployment             Show deployment information                        https://bosh.io/docs/cli-v2#deployment (aliases: dep)
  deployments            List deployments                                   https://bosh.io/docs/cli-v2#deployments (aliases: ds, deps)
  diff-config            Diff two configs by ID                             https://bosh.io/docs/cli-v2#diff-config
  disks                  List disks                                         https://bosh.io/docs/cli-v2#disks
  environment            Show environment                                   https://bosh.io/docs/cli-v2#environment (aliases: env)
  environments           List environments                                  https://bosh.io/docs/cli-v2#environments (aliases: envs)
  errands                List errands                                       https://bosh.io/docs/cli-v2#errands (aliases: es)
  event                  Show event details                                 https://bosh.io/docs/cli-v2#event
  events                 List events                                        https://bosh.io/docs/cli-v2#events
  export-release         Export the compiled release to a tarball           https://bosh.io/docs/cli-v2#export-release
  finalize-release       Create final release from dev release tarball      https://bosh.io/docs/cli-v2#finalize-release
  generate-job           Generate job                                       https://bosh.io/docs/cli-v2#generate-job
  generate-package       Generate package                                   https://bosh.io/docs/cli-v2#generate-package
  help                   Show this help message                             https://bosh.io/docs/cli-v2#help
  ignore                 Ignore an instance                                 https://bosh.io/docs/cli-v2#ignore
  init-release           Initialize release                                 https://bosh.io/docs/cli-v2#init-release
  inspect-release        List release contents such as jobs                 https://bosh.io/docs/cli-v2#inspect-release
  instances              List all instances in a deployment                 https://bosh.io/docs/cli-v2#instances (aliases: is)
  interpolate            Interpolates variables into a manifest             https://bosh.io/docs/cli-v2#interpolate (aliases: int)
  locks                  List current locks                                 https://bosh.io/docs/cli-v2#locks
  log-in                 Log in                                             https://bosh.io/docs/cli-v2#log-in (aliases: l, login)
  log-out                Log out                                            https://bosh.io/docs/cli-v2#log-out (aliases: logout)
  logs                   Fetch logs from instance(s)                        https://bosh.io/docs/cli-v2#logs
  manifest               Show deployment manifest                           https://bosh.io/docs/cli-v2#manifest (aliases: man)
  orphan-disk            Orphan disk                                        https://bosh.io/docs/cli-v2#orphan-disk
  recreate               Recreate instance(s)                               https://bosh.io/docs/cli-v2#recreate
  releases               List releases                                      https://bosh.io/docs/cli-v2#releases (aliases: rs)
  remove-blob            Remove blob                                        https://bosh.io/docs/cli-v2#remove-blob
  repack-stemcell        Repack stemcell                                    https://bosh.io/docs/cli-v2#repack-stemcell
  reset-release          Reset release                                      https://bosh.io/docs/cli-v2#reset-release
  restart                Restart instance(s)                                https://bosh.io/docs/cli-v2#restart
  run-errand             Run errand                                         https://bosh.io/docs/cli-v2#run-errand
  runtime-config         Show current runtime config                        https://bosh.io/docs/cli-v2#runtime-config (aliases: rc)
  scp                    SCP to/from instance(s)                            https://bosh.io/docs/cli-v2#scp
  snapshots              List snapshots                                     https://bosh.io/docs/cli-v2#snapshots
  ssh                    SSH into instance(s)                               https://bosh.io/docs/cli-v2#ssh
  start                  Start instance(s)                                  https://bosh.io/docs/cli-v2#start
  stemcells              List stemcells                                     https://bosh.io/docs/cli-v2#stemcells (aliases: ss)
  stop                   Stop instance(s)                                   https://bosh.io/docs/cli-v2#stop
  sync-blobs             Sync blobs                                         https://bosh.io/docs/cli-v2#sync-blobs
  take-snapshot          Take snapshot                                      https://bosh.io/docs/cli-v2#take-snapshot
  task                   Show task status and start tracking its output     https://bosh.io/docs/cli-v2#task (aliases: t)
  tasks                  List running or recent tasks                       https://bosh.io/docs/cli-v2#tasks (aliases: ts)
  unignore               Unignore an instance                               https://bosh.io/docs/cli-v2#unignore
  update-cloud-config    Update current cloud config                        https://bosh.io/docs/cli-v2#update-cloud-config (aliases: ucc)
  update-config          Update config                                      https://bosh.io/docs/cli-v2#update-config (aliases: uc)
  update-cpi-config      Update current CPI config                          https://bosh.io/docs/cli-v2#update-cpi-config
  update-resurrection    Enable/disable resurrection                        https://bosh.io/docs/cli-v2#update-resurrection
  update-runtime-config  Update current runtime config                      https://bosh.io/docs/cli-v2#update-runtime-config (aliases: urc)
  upload-blobs           Upload blobs                                       https://bosh.io/docs/cli-v2#upload-blobs
  upload-release         Upload release                                     https://bosh.io/docs/cli-v2#upload-release (aliases: ur)
  upload-stemcell        Upload stemcell                                    https://bosh.io/docs/cli-v2#upload-stemcell (aliases: us)
  variables              List variables                                     https://bosh.io/docs/cli-v2#variables (aliases: vars)
  vendor-package         Vendor package                                     https://bosh.io/docs/cli-v2#vendor-package
  vms                    List all VMs in all deployments                    https://bosh.io/docs/cli-v2#vms




















# Cloud Foundry Components

Cloud Foundry is a distributed system involving several different components. Distributed systems balance their processing loads over multiple networked machines.They are optimized between efficiency and resiliency against failures. Cloud Foundry is comprised of a modular distributed architecture with discrete components utilized for dedicated purposes.

### DISTRIBUTED SYSTEMS

As a distributed system, each Cloud Foundry component has a well-defined responsibility. The different components interact with one another to achieve a common goal. Distributed systems achieve component interaction through communicating via messages and using central data stores for system-wide state coordination. There are several benefits to using a distributed component model, such as the ability to scale a single component in isolation, or the ability to change one component without directly affecting another.
It is important for the operator to understand what comprises the Cloud Foundry distributed system. For example, some components are responsible for system state and you need to back them up. Other components are responsible for running your applications, and therefore you most probably want more than one instance of those components to remain running to ensure resiliency. Ultimately, understanding these components and where their boundaries of responsibility lie is vital when it comes to establishing concerns such as resiliency and disaster recovery.
In this chapter, you will learn about the following:
1.	The core components, including their purpose and boundary of responsibility
2.	The flow of communication and interaction between components
3.	The components responsible for state


| Layer	                                     |  Compoenents                                                    |
|------------------------------------------  | --------------------------------------------------------------- |
| Routing                                    | GoRouter, TCPRouter, and external load balancer                 |
| Authentication and user management         | Cloud Controller, Diego’s core components (e.g., BBS and Brain) |
| App storage and execution                  | blobstore, Diego Cell (Garden, and runC)                        |
| Services                                   | Service Broker, User Provided Service                           |
| Messaging                                  | NATS (Network Address Translation) Messaging Bus                |
| Metrics and logging                        |  Loggregator (including Doppler and the Firehose)               |

![services](https://github.com/Aslamlatheef/PivotalCloudfoundry/blob/master/PCF/Services.png)

All HTTP-based traffic first enters Cloud Foundry from an external load balancer fronting Cloud Foundry. The load balancer is primarily used for routing traffic to the GoRouter.
**LOAD BALANCER PREFERENCE**
The choice of load balancer is according to your preference. Infrastructure hosted on AWS often use Amazon’s elastic load balancer (ELB). On-premises deployments such as those on vSphere or OpenStack take advantage of existing enterprise load balancers such as F5’s BIG-IP.

The load balancer can either handle secure socket layer (SSL) decryption and then route traffic on to the GoRouter, or pass the SSL connection on to the GoRouter for SSL decryption.

The GoRouter receives all incoming HTTP traffic from the load balancer. The term “router” can be misleading to networking engineers who expect routers to implement specific networking standards. Conceptually, the router should be treated as a reverse proxy, responsible for centrally managing and routing all incoming HTTP(S) traffic to the appropriate component. Traffic will typically be passed on to either an application or the Cloud Controller:

•	Application users target their desired applications via a dedicated domain. The GoRouter will route application traffic to the appropriate AI running on a Diego Cell. If multiple AIs are running, the GoRouter will round-robin traffic across the AIs to distribute the workload.3
•	Cloud Foundry users address the Cloud Controller: Cloud Foundry’s API known as the CAPI. Some client traffic will go directly from the GoRouter to the UAA; however, most UAA calls are initiated from the Cloud Controller.

The GoRouter periodically queries Diego, Cloud Foundry’s container runtime system, for information on the location of the Cells and containers on which each application is currently running.

Applications require a route for external traffic to access them. The GoRouter uses a routing table to keep track of the available applications. Because applications can have multiple AIs, all with a single route, each route has an associated array of host:port entries. The host is the Diego Cell machine running the application. The GoRouter regularly recomputes new routing tables based on the Cell’s IP addresses and the host-side port numbers for the Cell’s containers.




**User Management and the UAA**

As traffic enters Cloud Foundry, it needs to be authenticated. Cloud Foundry’s UAA service is the central identity management service for managing:
•	Cloud Foundry developers
•	Application clients/end users
•	Applications requiring application-to-application interactions

The UAA is an OAuth2 authorization server, that issues access tokens for client applications to use when they act on behalf of Cloud Foundry users; for example, when they request access to platform resources. The UAA is based on the most up-to-date security standards like OAuth, OpenID Connect, and System for Cross-domain Identity Management (SCIM). It authenticates platform users via their Cloud Foundry credentials. When users register an account with the Cloud Foundry platform, the UAA acts as the user identity store, retaining user passwords in the UAA database. The UAA can also act as a SSO service. It has endpoints for managing user accounts and registering OAuth2 clients as well as various other management functions. In addition, you can configure the UAA’s user-identity store to either store user information internally or connect to an external user store through LDAP or SAML. Here are a couple of examples:

•	Users can use LDAP credentials to gain access to the Cloud Foundry platform instead of registering a separate account.
•	Operators can use SAML to connect to an external user store in order to enable SSO for users who want to access Cloud Foundry.

The UAA has its own database known as the UAADB. Like all databases responsible for system state, this is a critical component and you must make backups.
**The Cloud Controller**

The Cloud Controller exposes Cloud Foundry’s REST API. Users of Cloud Foundry target the Cloud Controller so that they can interact with the Cloud Foundry API (CAPI). Clients directly interact with the Cloud Controller for tasks such as these:
•	Pushing, staging, running, updating, and retrieving applications
•	Pushing, staging, and running discrete one-off tasks

***You can interact with the Cloud Controller in the following three ways:***
•	A scriptable CLI
•	Language bindings (currently Java)
•	Integration with development tools (IDEs) to ease the deployment process

You can find a detailed overview of the API commands at API Docs. The V3 API extends the API to also include Tasks in addition to applications LRPs.

The Cloud Controller is responsible for the System State and the Application Life-Cycle Policy.

System State
The Cloud Controller uses two components for storing state: a blobstore and a database known as the CCDB.
THE CLOUD CONTROLLER BLOBSTORE

To store large binary files such as application artifacts and staged application droplets, Cloud Foundry’s Cloud Controller uses a blobstore that holds different types of artifacts:
•	Application code packages—unstaged files that represent an application
•	Resource files
•	Buildpacks
•	Droplets and other container images

Resource files are uploaded to the Cloud Controller and then cached in the blobstore with a unique secure hash algorithm (SHA) so as to be reused without reuploading the file. Before uploading all the application files, the Cloud Foundry CLI issues a resource match request to the Cloud Controller to determine if any of the application files already exist in the resource cache. When the application files are uploaded, the Cloud Foundry CLI omits files that exist in the resource cache by supplying the result of the resource-match request. The uploaded application files are combined with the files from the resource cache to create the complete application package.

Droplets are the result of taking an app package and staging it via a processing buildpack to create an executable binary artifact. The blobstore uses FOG so that it can use abstractions like Amazon Simple Storage Service (Amazon S3) or a mounted network file system (NFS) for storage.

***THE CCDB***

The Cloud Controller, via its CCDB, also maintains records of the logical hierarchical structure including available orgs, spaces, apps, services, service instances, user roles, and more. It maintains users’ information and how their roles map to orgs and spaces.

***The Application Life-Cycle Policy***

The Cloud Controller is responsible for the Application Life-Cycle Policy. Conceptually, the Cloud Controller is Cloud Foundry’s CPU that drives the other components. For example, when you use the cf push command to push an application or task to Cloud Foundry, the Cloud Controller stores the raw application bits in its blobstore, creates a record to track the application metadata in its database, and directs the other system components to stage and run the application.
The Cloud Controller is application and task centric. It implements all of the object modeling around running applications (handling permissions, buildpack selection, service binding, etc.). The Cloud Controller is concerned with policy; for example, “run two instances of my application,” but the responsibility of orchestration and execution has been passed to Diego.

***CONTINUOUS DELIVERY PIPELINES***
Many companies choose to interact with Cloud Foundry through a continuous delivery pipeline such as Concourse.ci that uses machines to utilize the CAPI. This approach reduces human error and offers deployment repeatability.
Application Execution

The components responsible for executing your applications and tasks include Diego, Garden (a container management API), and an Open Container Initiative (OCI)–compatible4 backend container implementation (like runC).

***Diego***
Diego is the container runtime architecture for Cloud Foundry. Whereas the Cloud Controller is concerned with policy, it is Diego that provides the scheduling, orchestration, and placement of applications and tasks. Diego is designed to keep applications available by constantly monitoring their states and reconciling the actual system state with the expected state by starting and stopping processes as required.
***Garden and runC***
Garden is a platform-agnostic Go API for container creation and management. Garden has pluggable backends for different platforms and runtimes, including Linux, Windows, and runC, an implementation of the OCI specification. 

***Metrics and Logging***
As discussed in “Aggregated Streaming of Logs and Metrics”, system logs and metrics are continually generated and streamed from every component. In addition, AIs should produce logs as a continual stream for each running AI. Cloud Foundry’s logging system is known as the Loggregator. The Loggregator aggregates component metrics and application logs into a central location using a Metron agent.

***Metron Agent***
The Metron agent comes from the Loggregator subsystem and resides on every VM. It gathers a mix of metrics and log statistics from the Cloud Foundry components; for example, the Metron agent gathers application logs from the Cloud Foundry Diego hosts known as Cells. Operators can use the collected component logs and metrics to monitor a Cloud Foundry deployment. These metrics and logs can be forwarded by a syslog forwarder onto a syslog drain. It is possible to drain syslogs into multiple consumers. You can set up as many syslog “sinks” for an application as you want. Each sink will receive all messages.
Metron has the job of forwarding application logs and component metrics to the Loggregator subsystem by taking traffic from the various emitter sources (Cells in the case of apps) and routing that logging traffic to one or more Loggregator components. An instance of the Metron agent runs on each VM in a Cloud Foundry system and logs are therefore co-located on the emitter sources.
***Loggregator***
The Loggregator (log aggregator) system continually streams logging and metric information. The Loggregator’s Firehose provides access to application logs, container metrics (memory, CPU, and disk-per-app instance), some component metrics, and component counter/HTTP events. If you want to see firsthand the output from the Loggregator, you can invoke the CF CLI command      * *$ cf logs APP* *
The Firehose does not provide component logs. Component logs are retrieved through an rsyslog drain.

***Messaging***
Most component machines communicate with one another internally through HTTP and HTTPS protocols. Temporary messages and data are captured in two locations:

•	A Consul server stores the longer-lived control data such as component IP addresses and distributed locks that prevent components from duplicating actions.
•	Diego’s bulletin board system (BBS) stores a real-time view of the system, including the more frequently updated and disposable data such as Cell and application status, unallocated work, and heartbeat messages.

The Route-Emitter component still uses the NATS messaging system, a lightweight distributed publish–subscribe message queuing system, to broadcast the latest routing tables to the routers.

***Additional Components***
In addition to the aforementioned core system components, there are other components that comprise the Cloud Foundry ecosystem.

***Stacks***
A stack is a prebuilt root filesystem (rootfs). Stacks are used along with droplets (the output of buildpack staging). They provide the container filesystem used for running applications.
Cells can support more than one stack if configured correctly; however, a Cell must ensure buildpack and stack compatibility. For example, a Windows “stack” cannot run on Linux VMs. Therefore, to stage or run a Linux app, a Cell running a Linux stack must be available (and have free memory).

***A Marketplace of On-Demand Services***
Applications often depend on additional backing services such as databases, caches, messaging engines, or third-party APIs. Each Cloud Foundry deployment has the concept of a marketplace. The Cloud Foundry marketplace is a platform extension point. It exposes a set of services that are available for developers to use in support of running applications. Developers do not build applications in isolation. Applications often require additional middleware services such as data persistence, search, caching, graphing, messaging, API management, and more.

The platform operator exposes additional services to the marketplace through service brokers, route services, and user-provided services. The marketplace provides Cloud Foundry users with the self-service, on-demand provisioning of additional service instances. The platform operator can expose different service plans to different Cloud Foundry Orgs. Developers are able to view and create service instances only for service plans that have been configured to be visible for their targeted Org and Space. You can make service plans public and visible to all, or private to limit service visibility.

A service can offer different service plans to provide varying levels of resources or features for the same service. An example service plan is a database service offering small, medium, or large plans with differing levels of concurrent connections and storage sizes. The provisioned service provides a unique set of credentials that can be used to bind and connect an application to the service.

Services not only enhance applications through providing middleware; they are concerned with all possible components to enable development teams to move quickly, including, for example, GitHub, Pivotal Tracker, CI services, and route services. For example, you can expose to the marketplace any application running on the platform that offers a service for others to consume. One advantage of this approach is that the service broker plans can prepopulate datastores with a specific schema or set of data (such as a sample customer set required for unit testing). Another example could be a service broker plan to provide specific preconfigured templates for apps.

***SERVICE BROKERS***
Developers can provision service instances and then bind those instances to an application via a service broker responsible for providing the service instance.
A service broker interacts with the Cloud Controller to provision a service instance. Service brokers advertise a catalog of service offerings and service plans (e.g., a single-node MySQL plan or a clustered multinode MySQL plan). A service broker implements the CAPI to provide the user with the following:
•	List service offerings
•	Provision (create) and deprovision (delete) service instances
•	Enable applications to bind to, and unbind from, the service instances
In general, provision reserves service resources (e.g., creates a new VM) and bind delivers the required information for accessing the resource to an application. The reserved resource is known, in Cloud Foundry parlance, as a service instance.
The service instance is governed by the broker author. What a service instance represents can vary not just by service, but also by plan. For example, an instance could be a container, a VM, or a new table and user in an existing database. Plans could offer a single database on a multitenant server, a dedicated datastore cluster, or simply an account and specific configuration on a running Cloud Foundry application. The key concern is that the broker implements the required API to interact with the Cloud Controller.

***USER-PROVIDED SERVICES***
In addition to Cloud Foundry-managed service instances, operators can expose existing services via user-provided services. This allows established services such as a customer database to be bound to Cloud Foundry applications.
Buildpacks and Docker Images
How do you run applications inside Cloud Foundry? Applications can simply be pushed through the following CF CLI command:
$ cf push
There are currently two defined artifact types that you can cf push:
•	A standalone application
•	A prebuilt Docker image (that could contain additional runtime and middleware dependencies)

You can push standalone applications in both the form of a prebuild artifact such as a war/jar file or, in some cases, via raw source code such as a link to a Git remote.

Because a standalone application is not already part of a container image, when it is pushed to Cloud Foundry the buildpack process runs a compile phase. This involves compiling any source code and packaging the application and runtime dependencies into an executable container image. The buildpack process is also responsible for constructing the application runtime environment, deploying the application, and, finally, starting the required processes.

Buildpacks are a core link in the chain of the Cloud Foundry deployment process if you are deploying only an application artifact (e.g., JAR, WAR, Ruby, or Go source). The buildpack automates the following:
•	The detection of an application framework
•	The application compilation (known in Cloud Foundry terminology as staging)
•	Running the application

The officially supported buildpacks are listed at http://docs.cloudfoundry.org/buildpacks/index.html. This list includes Ruby, Java (including other JVM-based languages), Go, Python, PHP, Node.js, and the binary and staticfile buildpacks.
Numerous additional community buildpacks exist. You can extend buildpacks or create new ones for specific language, framework, and runtime support. For example, the reason you might extend the Java buildpack (JBP) is to add support for additional application servers or a specific monitoring agent.
Buildpacks take your application artifact and containerize it into what Cloud Foundry calls a droplet. However, if you already have an OCI-compatible container image such as a Docker image, you can use cf push to move that directly to Cloud Foundry in order to run it. Containerization is the delivery mechanism for applications. This is true whether you push an application that Cloud Foundry containerizes into a droplet, or you push a prebuilt Docker image.
Infrastructure and the Cloud Provider Interface

Cloud Foundry relies on existing cloud-based infrastructure. The underlying infrastructure will have implementation-specific details. For example, vSphere’s vCenter deals with clusters and resource pools, whereas AWS deals with regions and AZs. There are, however, some fundamental capabilities that need to be available and set up prior to installing Cloud Foundry:

•	Networks and subnets (typically a /22 private network)
•	VMs with specified CPU and memory requirements
•	Storage for VMs
•	File server or blobstore
•	DNS, certificates, and wildcard domains
•	Load balancer to pass traffic into the GoRouter
•	NAT5 for traffic flowing back to the load balancer

  

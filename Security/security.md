# Security

Cloud Foundry offers significant security benefits over traditional approaches to deploying applications because it allows you to strengthen your security posture once, for all applications deployed to the platform. However, securing distributed systems involves inherent complexity. For example, think about these issues:

- How much effort is required to automatically establish and apply network traffic rules to isolate components?
- What policies should be applied to automatically limit resources in order to defend against denial-of-service (DoS) attacks?
- How do you implement role-based access controls (RBAC) with in-built auditing of system access and actions?
- How do you know which components are potentially affected by a specific vulnerability and require patching?
- How do you safely patch the underlying OS without incurring application downtime?

These issues are standard requirements for most systems running in corporate data centers. The more custom engineering you use, the more you need to secure and patch that system. Distributed systems increase the security burden because there are more moving parts, and with the advances in container technology, new challenges arise, such as “How do you dynamically apply microsegmentation at the container layer?” Additionally, when it comes to rolling out security patches to update the system, many distributed systems suffer from configuration drift—namely, the lack of consistency between supposedly identical environments. Therefore, when working with complex distributed systems (specifically any cloud-based infrastructure), environmental risk factors are intensified

### THE CHALLENGE OF CONFIGURATION DRIFT
Deployment environments (such as staging, quality assurance, and production) are often complex and time-consuming to construct and administer, producing the ongoing challenge of trying to manage configuration drift to maintain consistency between environments and VMs. Reproducible consistency through release-engineering tool chains such as Cloud Foundry’s BOSH addresses this challenge.

***Environmental Risk Factors for Advanced Persistent Threats***
Malware known as advanced persistent threats (APTs) needs three risk factors in order to thrive:

1. Time
2. Leaked or misused credentials
3. Misconfigured and/or unpatched software

Given enough time, APTs can observe, analyze, and learn what is occurring within your system, storing away key pieces of information at will. If APTs obtain credentials, they can then further access other systems and data such as important ingress points into your protected data layer. Finally, unpatched software vulnerabilities provide APTs the freedom to further exploit, compromise, and expose your system

***Challenge of Minimal Change***

There has been a belief that if enterprises deliver software with velocity, the trade-off is they must reduce their security posture and increase risk. Therefore, traditionally, many enterprises have relied on a concept of minimal change to mitigate risk and reduce velocity. Security teams establish strict and restrictive policies in an attempt to minimize the injection of new vulnerabilities. This is evident by ticketing systems to make basic configuration changes to middleware and databases, long-lived transport layer security (TLS) credentials, static firewall rules, and numerous security policies to which applications must adhere.

Minimal change becomes compounded by complexity of the environment. Because machines are difficult to patch and maintain, environmental complexity introduces a significant lag between the time a vulnerability is discovered and the time a machine is patched, be it months, or worse, even years in some production enterprise environments.


***The Three Rs of Enterprise Security***

These combined risk factors provide a perfect ecosystem in which APTs can flourish, and minimal change creates an environment in which all three factors are likely to occur. Cloud Foundry inverts the traditional enterprise security model by focusing on the three Rs of enterprise security: rotate, repave, repair.2"
  
  1. Rotate the credentials frequently so that they are valid only for short periods of time.
  2. Repave (rebuild) servers and applications from a known good state to cut down on the amount of time an attack can live.
  3. Repair vulnerable software as soon as updates are available.
  
For the three Rs to be effective in minimizing the APT risk factors, you need to implement them repeatedly at high velocity. For example, data center credentials can be rotated hourly, servers and applications can be rebuilt several times a day, and complete vulnerability patching can be achieved within hours of patch availability. With this paradigm in mind, faster now equates to a safer and stronger security posture.

***ADDITIONAL CLOUD FOUNDRY SECURITY***

Cloud Foundry, along with BOSH and continuous integration (CI) tooling, provides tooling to make the three Rs’ security posture a reality. Additionally, Cloud Foundry further protects you from security threats by automatically applying the following additional security controls to isolate applications and data:
- Manages software-release vulnerability by using new Cloud Foundry releases created with timely updates to address code issues
- Manages OS vulnerability by using a new OS created with the latest security patches
- Implements RBACs, applying and enforcing roles and permissions to ensure that users of the platform can view and affect only the resources to which they have been granted access
- Secures both code and the configuration of an application within a multitenant environment
- Deploys each application within its own self-contained and isolated containerized environment
- Prevents possible DoS attacks through resource starvation
- Provides an operator audit trail showing all operator actions applied to the platform
- Provides a user audit trail recording all relevant API invocations of an application
- Implements network traffic rules (security groups) to prevent system access to and from external networks, production services, and between internal components

BOSH and the underlying infrastructure expands the security posture further by handling data-at-rest encryption support through the infrastructure layer, usually by some device mechanism or filesystem-level support. For example, BOSH can use AWS EBS (Elastic Block Store) volume encryption for persistent disks.

Because every component within Cloud Foundry is created with the same OS image, Cloud Foundry eases the burden of rolling out these OS and software-release updates by using BOSH. BOSH redeploys updated VMs, component by component, to ensure zero-to-minimal downtime. This ultimately removes patching and updating concerns from the operator and provides a safer, more resilient way to update Cloud Foundry while keeping applications running. It is now totally possible to rebuild every VM in your data center from a known good state, as desired, with zero application downtime.

In addition, you can rebuild and redeploy the applications themselves from a known good release, upon request, with zero downtime. These rebuilding, repairing, and redeploying capabilities ensure that the patch turnaround time for the entire stack is as fast and as encompassing as possible, reaching every affected component across the stack with minimal human intervention. Cadence is limited only by the time it takes to run the pipeline and commit new code.

In addition to patching, if for any reason a component becomes compromised, you can instantly recreate it by using a known and clean software release and OS image, and move the compromised component into a quarantine area for further inspection.
There are additional detailed technical aspects that further improve security; for example, using namespaces for all containerized processes. I suggest reviewing the individual components for a more detailed understanding of how components such as Garden or the UAA help to further increase the security posture.

### UAA Management

Role-based access defines who can use the platform and how. Cloud Foundry uses RBAC, with each role granting permissions to a specific environment the user is targeting. All collaborators target an environment with their individual user accounts associated with a role that governs what level and type of access the user has within that environment. Cloud Foundry’s UAA is the central identity management service for both users and applications. It supports federated login, Lightweight Directory Access Protocol (LDAP), Security Assertion Markup Language (SAML), SSO, and multifactor authentication. UAA is a powerful component for strengthening your security posture for both user and application authentication

### Organizations and Spaces

Most developers are familiar with using VMs for development and deployment. Cloud Foundry is a virtualization layer (underpinned by containers) on top of a virtualization layer underpinned by VMs. Therefore, users do not have direct access to a specific machine or VM; rather, they simply access a logical partition of resources to deploy their apps.

To partition and allocate resources, Cloud Foundry uses logical boundaries known as Organizations (Orgs) and Spaces. Orgs contain one or more Spaces. Users can belong to any number of Orgs and/or Spaces, and users can have unique roles and permissions in each Org or Space to which they belong.

Orgs and Spaces provide the following:
- Logical separation and assignment of Cloud Foundry resources
- Isolation between different teams
- Logical isolation of development, test, staging, and production environments

For some enterprise customers with traditional silos, defining their required Orgs and Spaces can at first seem complex. Ideally, development teams should have autonomy to create and manage their own Spaces, as required. For development teams embracing microservices, the best approach is to organize teams by the big-A application—meaning a group of related (small-a) applications or services that can collectively and appropriately be grouped together, often referred to as bulkheading.
Ultimately, you know your business and how your developers work, so use these logical structures to provide meaningful working environments and pipelines for your developers.

***Orgs***
An Org is the top level of separation. A logical mapping could be to your business unit, a big-A application, or some other reasonable bounded context. When working with large enterprises that might have 200 developers in a business unit, I normally try to shift their thinking to the “Two-Pizza Team” model for application development. However, the actual number of developers within an Org or Space should not be a contentious point if it maps well to the physical organization and does not impede development or deployment.

***Spaces***
Every application and service is scoped to a Cloud Foundry Space. A Space provides a shared location that a set of users can access for application development, deployment, and maintenance. Every Space belongs to one Org. Each Org contains at least one Space but could contain several, and therefore can contain a broader set of collaborators than a single Space.


### ENVIRONMENT VARIABLES FOR PROPERTIES
The cf push command is the user saying to Cloud Foundry, “Here is my application artifact; run it on the cloud for me. I do not care how!”

The “I do not care how” needs explaining. There are properties you should care about, and these properties are configured by environment variables. Cloud Foundry uses environment variables to inform a deployed application about its environment. Environment variables include the following:

-	How much memory to use
-	What routes should be bound to the application
-	How many instances of an application should be run
-	Additional app-specific environment properties

*** The Cloud Foundry user can configure all of these propertie***

Spaces are more of a developer concern. I believe there should be a limit to the amount of developers in a Space because there is a requirement for a level of trust due to the scope of shared resources and exposed environment variables that reside at the Space level. For the hyper security-conscious who have no trust between application teams, one Space per application is the only way forward. In reality, a Space for a big-A application might be more appropriate.
### COLOCATION AND APPLICATION INTERACTIONS

When considering the logical separations of your Cloud Foundry deployment, namely what Orgs and Spaces to construct, it is important to consider your application-to-application and application-to-services interactions. Although this consideration is more of a microservices consideration, an understanding of application and service boundaries is beneficial in understanding any colocation requirements. An example of this would be an application needing to access a corporate service in a specific data center or network. These concerns and their subsequent impacts become more noticeable at scale. You will need to design, understand, and document service discovery and app-to-app dependency, and additional frameworks such as Spring Cloud can significantly help here.

### Resource Allocation
In addition to defining team structures, you can use Orgs and Spaces for assigning appropriate resources to each team.
Collaborators in an Org share the following:
- Resource quota
- Applications
- Services availability
- Custom domains
### Domains Hosts and Routes

To enable traffic from external clients, applications require a specific URL known as a route. A route is a URL comprised of a domain and an optional host as a prefix. The host in this context is the portion of the URL referring to the application or applications, such as these:
  - my-app-name is the host prefix
  - aslamlatheef.com is the domain
  - my-app-name.aslamlatheef.com is the route

### Route

Application consumers use a route to access an application. Each route is directly bound to one or more applications in Cloud Foundry. When running multiple instances, Cloud Foundry automatically load balances application traffic across multiple AIs through a component called the GoRouter. Because individual AIs can come and go for various reasons (scaling, app deletion, app crash), the GoRouter dynamically updates its routing table. Dead routes are automatically pruned from the routing table and new routes are added when they become available. Dynamic routing is a powerful feature. Traditional manual route table maintenance can be slow because it often requires submitting tickets to update or correct domain name server (DNS) or load balancer components. Chapter 7 discusses these various routing concepts further.

### Domains

Domains provide a namespace from which to create routes. Cloud Foundry uses domains within routes to direct requests to specific applications. You can also register and use a custom domain, known as an owned domain. Domains are associated with Orgs and are not directly bound to applications. Domains can be shared, meaning that they are registered to multiple Orgs, or private, registered to only one Org. Owned domains are always private.
Context Path–Based Routing

A context path in a URL extends a top-level route with additional context so as to route the client to either specific application functionality or a different application. For example, http://my-app-name.aslamlatheef.comcan be extended to http://my-app-name.aslamlatheef.com/home to direct a client to a homepage.

In the preceding example, your clients can reach the application via my-app-name.aslamlatheef.com. Therefore, if a client targets that route and uses a different context path, it will still reach only a single application. For example, http://my-app-name.aslamlatheef/home and http://my-app-name.aslamlatheef.com/aboutme both be routed by GoRouter to your app my-app-name.
This approach works if all the functionality under the route my-app-name.aslamlatheef.com can be served by a single app. However, when using microservices, there is a need to have a unified top-level route that can be backed by a number of microservices. Each service uses the same top-level domain but can be individually reached by different paths in the URL. The microservices collectively serve all supported paths under the domain my-app-name.aslamlatheef.com. With context path–based routing, you can independently scale those portions of your app that are experiencing high/low traffic.

### Rolling Upgrades and Blue/Green Deployments
As discussed in “Security”, both the applications running on the platform and the platform itself allow for rolling upgrades and zero-downtime deployment through a distributed consensus.
You can update applications running on the platform with zero downtime through a technique known as blue/green deployments.




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

 **Deploying and scaling applications are completely independent operations**








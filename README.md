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


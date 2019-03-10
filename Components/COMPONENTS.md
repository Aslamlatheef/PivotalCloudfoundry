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


  

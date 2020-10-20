# Application Reliability

# Navigation Menu
- [Application Assessment Checklist](#Application-Assessment-Checklist)
  - [Application Design](#Application-Design)
    - [Design](#Design)
    - [Failure Mode Analysis](#Failure-Mode-Analysis)
    - [Targets &amp; Non Functional Requirements](#Targets--Non-Functional-Requirements)
    - [Dependencies](#Dependencies)
  - [Health Modelling](#Health-Modelling)
    - [Resource/Infrastructure Level Monitoring](#ResourceInfrastructure-Level-Monitoring)
    - [Data Interpretation &amp; Health Modelling](#Data-Interpretation--Health-Modelling)
    - [Alerting](#Alerting)
    - [Monitoring and Measurement](#Monitoring-and-Measurement)
  - [Capacity &amp; Service Availability](#Capacity--Service-Availability)
    - [Service Availability](#Service-Availability)
    - [Capacity](#Capacity)
  - [Application Platform Availability](#Application-Platform-Availability)
    - [Service SKU](#Service-SKU)
    - [Compute Availability](#Compute-Availability)
  - [Data Platform Availability](#Data-Platform-Availability)
    - [Service SKU](#Service-SKU)
    - [Consistency](#Consistency)
    - [Replication and Redundancy](#Replication-and-Redundancy)
  - [Networking &amp; Connectivity](#Networking--Connectivity)
    - [Connectivity](#Connectivity)
    - [Zone-Aware Services](#ZoneAware-Services)
  - [Scalability &amp; Performance](#Scalability--Performance)
    - [App Performance](#App-Performance)
    - [Data Size/Growth](#Data-SizeGrowth)
    - [Data Latency and Throughput](#Data-Latency-and-Throughput)
    - [Network Throughput and Latency](#Network-Throughput-and-Latency)
    - [Elasticity](#Elasticity)
  - [Security &amp; Compliance](#Security--Compliance)
    - [Identity and Access](#Identity-and-Access)
    - [Security Center](#Security-Center)
    - [Network Security](#Network-Security)
  - [Operational Procedures](#Operational-Procedures)
    - [Recovery &amp; Failover](#Recovery--Failover)
    - [Scalability &amp; Capacity Model](#Scalability--Capacity-Model)
    - [Configuration &amp; Secrets Management](#Configuration--Secrets-Management)
  - [Deployment &amp; Testing](#Deployment--Testing)
    - [Application Deployments](#Application-Deployments)
    - [Build Environments](#Build-Environments)
    - [Testing &amp; Validation](#Testing--Validation)



# Application Assessment Checklist
## Application Design
    
### Design
            
* Does the application support multi-region deployments?


  _Multiple regions should be used for failover purposes in a disaster state, as part of either re-deployment, warm-spare active-passive, or hot-spare active-active strategies([Failover strategies](https://docs.microsoft.com/en-us/azure/availability-zones/az-overview#availability-zones))_
* Within a region is the application architecture designed to use Availability Zones?


  _Availability Zones can be used to optimise application availability within a region by providing datacenter level fault tolerance. However, the application architecture must not share dependencies between zones to use them effectively. It is also important to note that Availability Zones may introduce performance and cost considerations for applications which are extremely 'chatty' across zones given the implied physical separation between each zone and inter-zone bandwidth charges([Availability Zones](https://docs.microsoft.com/en-us/azure/availability-zones/az-overview#availability-zones))_
* Is component proximity required for application performance reasons?


  _If all or part of the application is highly sensitive to latency it may mandate component co-locality which can limit the applicability of multi-region and multi-zone strategies_
* Can the application operate with reduced functionality or degraded performance in the presence of an outage?


  _Avoiding failure is impossible in the public cloud, and as a result applications require resilience to respond to outages and deliver reliability. The application should therefore be designed to operate even when impacted by regional, zonal, service or component failures across critical application scenarios and functionality_
* Is the application designed to use managed services?


  _Azure managed services provide native resiliency capabilities to support overall application reliability, and where possible platform as a service offerings should be used to leverage these capabilities([Use managed services](https://docs.microsoft.com/en-us/azure/architecture/guide/design-principles/managed-services))_
* Has the application been designed to scale-out?


  _Azure provides elastic scalability, however, applications must leverage a scale-unit approach to navigate service and subscription limits to ensure that individual components and the application as a whole can scale horizontally([Design to scale out](https://docs.microsoft.com/en-us/azure/architecture/guide/design-principles/scale-out))_
* Is the application deployed across multiple Azure subscriptions?


  _Understanding the subscription landscape of the application and how components are organized within or across subscriptions is important when analyzing if relevant subscription limits or quotas can be navigated_
* Is an availability strategy defined? i.e. multi-geo, full/partial


  _An availability strategy should capture how the application remains available when in a failure state and should apply across all application components and the application deployment stamp as a whole such as via multi-geo scale-unit deployment approach_
* Has a Business Continuity Disaster Recovery (BCDR) strategy been defined for the application and/or its key scenarios?


  _A disaster recovery strategy should capture how the application responds to a disaster situation such as a regional outage or the loss of a critical platform service, using either a re-deployment, warm-spare active-passive, or hot-spare active-active approach_
### Failure Mode Analysis
            
* Has pathwise analysis been conducted to identify key flows within the application?


  _Pathwise analysis can be used to decompose a complex application into key flows to which business impact can be attached. Frequently, these key flows can be used to identify business critical paths within the application to which reliability targets are most applicable_
* Have all fault-points and fault-modes been identified?


  _Fault-points describe the elements within an application architecture which are capable of failing, while fault-modes capture the various ways by which a fault-point may fail. To ensure an application is resilient to end-to-end failures, it is essential that all fault-points and fault-modes are understood and operationalized([Failure Mode Analysis for Azure applications](https://docs.microsoft.com/en-us/azure/architecture/resiliency/failure-mode-analysis))_
* Have all single points of failure been eliminated?


  _A single point of failure describes a specific fault-point which if it where to fail would bring down the entire application. Single points of failure  introduce significant risk since any failure of this component will cause an application outage([Make all things redundant](https://docs.microsoft.com/en-us/azure/architecture/guide/design-principles/redundancy))_
* Have all 'singletons' been eliminated?


  _A 'singleton' describes a logical component within an application for which there can only ever be a single instance. It can apply to stateful architectural components or application code constructs. Ultimately, singletons introduce a significant risk by creating single points of failure within the application design_
### Targets &amp; Non Functional Requirements
            
* Are availability targets such as Service Level Agreements (SLAs), Service Level Indicators (SLIs), and Service Level Objectives (SLOs) defined for the application and/or key scenarios?


  _Understanding customer availability expectations is vital to reviewing overall operations for the application. For instance, if a customer is striving to achieve an application SLO of 99.999%, the level of inherent operational actionality required by the application is going to far greater than if an SLO of 99.9% was the aspiration_
  > Having clearly defined availability targets is crucial in order to have a goal to work and measure against. This will also determine which services an application can leverage vs. those which do not qualify in terms of the SLA they offer.
    - Are availability targets such as Service Level Agreements (SLAs), Service Level Indicators (SLIs), and Service Level Objectives (SLOs) defined for the application and/or key scenarios?


      _Understanding customer availability expectations is vital to reviewing overall operations for the application. For instance, if a customer is striving to achieve an application SLO of 99.999%, the level of inherent operational actionality required by the application is going to far greater than if an SLO of 99.9% was the aspiration_

      > Having clearly defined availability targets is crucial in order to have a goal to work and measure against. This will also determine which services an application can leverage vs. those which do not qualify in terms of the SLA they offer.
    - Are availability targets such as Service Level Agreements (SLAs), Service Level Indicators (SLIs), and Service Level Objectives (SLOs) defined for the application and/or key scenarios?


      _Understanding customer availability expectations is vital to reviewing overall operations for the application. For instance, if a customer is striving to achieve an application SLO of 99.999%, the level of inherent operational actionality required by the application is going to far greater than if an SLO of 99.9% was the aspiration_

      > Having clearly defined availability targets is crucial in order to have a goal to work and measure against. This will also determine which services an application can leverage vs. those which do not qualify in terms of the SLA they offer.
    - Are availability targets such as Service Level Agreements (SLAs), Service Level Indicators (SLIs), and Service Level Objectives (SLOs) defined for the application and/or key scenarios?


      _Understanding customer availability expectations is vital to reviewing overall operations for the application. For instance, if a customer is striving to achieve an application SLO of 99.999%, the level of inherent operational actionality required by the application is going to far greater than if an SLO of 99.9% was the aspiration_

      > Having clearly defined availability targets is crucial in order to have a goal to work and measure against. This will also determine which services an application can leverage vs. those which do not qualify in terms of the SLA they offer.
    - Are availability targets such as Service Level Agreements (SLAs), Service Level Indicators (SLIs), and Service Level Objectives (SLOs) defined for the application and/or key scenarios?


      _Understanding customer availability expectations is vital to reviewing overall operations for the application. For instance, if a customer is striving to achieve an application SLO of 99.999%, the level of inherent operational actionality required by the application is going to far greater than if an SLO of 99.9% was the aspiration_

      > Having clearly defined availability targets is crucial in order to have a goal to work and measure against. This will also determine which services an application can leverage vs. those which do not qualify in terms of the SLA they offer.
    - Are availability targets such as Service Level Agreements (SLAs), Service Level Indicators (SLIs), and Service Level Objectives (SLOs) defined for the application and/or key scenarios?


      _Understanding customer availability expectations is vital to reviewing overall operations for the application. For instance, if a customer is striving to achieve an application SLO of 99.999%, the level of inherent operational actionality required by the application is going to far greater than if an SLO of 99.9% was the aspiration_

      > Having clearly defined availability targets is crucial in order to have a goal to work and measure against. This will also determine which services an application can leverage vs. those which do not qualify in terms of the SLA they offer.
* Are recovery targets such as Recovery Time Objective (RTO) and Recovery Point Objective (RPO) defined for the application and/or key scenarios?


  _Understanding customer reliability expectations is vital to reviewing the overall reliability of the application. For instance, if a customer is striving to achieve an application RTO of less than a minute then back-up based and active-passive disaster recovery strategies are unlikely to be appropriate 
Recovery time objective (RTO): The maximum acceptable time the application is unavailable after a disaster incident 
Recovery point objective (RPO): The maximum duration of data loss that is acceptable during a disaster event_
  > Recovery targets should be defined in accordance to the required RTO and RPO targets for the workloads.
### Dependencies
            
* Are all internal and external dependencies identified and categorized as either weak or strong?


  _Internal dependencies describe components within the application scope which are required for the application to fully operate, while external dependencies captures required components outside the scope of the application, such as another application or third-party service._
  > Dependencies may be categorized as either strong or weak based on whether or not the application is able to continue operating in a degraded fashion in their absence. ([Twelve-Factor App: Dependencies](https://12factor.net/dependencies))
    - Are all internal and external dependencies identified and categorized as either weak or strong?


      _Internal dependencies describe components within the application scope which are required for the application to fully operate, while external dependencies captures required components outside the scope of the application, such as another application or third-party service._

      > Dependencies may be categorized as either strong or weak based on whether or not the application is able to continue operating in a degraded fashion in their absence. ([Twelve-Factor App: Dependencies](https://12factor.net/dependencies))
    - Are all internal and external dependencies identified and categorized as either weak or strong?


      _Internal dependencies describe components within the application scope which are required for the application to fully operate, while external dependencies captures required components outside the scope of the application, such as another application or third-party service._

      > Dependencies may be categorized as either strong or weak based on whether or not the application is able to continue operating in a degraded fashion in their absence. ([Twelve-Factor App: Dependencies](https://12factor.net/dependencies))
* Are SLAs and support agreements in place for all critical dependencies?


  _Service Level Agreement (SLA) represents a commitment around performance and availability of the application. Understanding the SLA of individual components within the system is essential in order to define reliability targets._
  > The operational commitments of all external and internal dependencies should be understood to inform the broader application operations and health model.
* Are all platform-level dependencies identified and understood?


  _The usage of platform level dependencies such as Azure Active Directory must also be understood to ensure that their availability and recovery targets align with that of the application_
* Can the application operate in the absence of its dependencies?


  _If the application has strong dependencies which it cannot operate in the absence of, then the availability and recovery targets of these dependencies should align with that of the application itself. Effort should be taken to minimize dependencies to achieve control over application reliability([Minimize dependencies](https://docs.microsoft.com/en-us/azure/architecture/guide/design-principles/minimize-coordination))_
* Is the lifecycle of the application decoupled from its dependencies?


  _If the application lifecycle is closely coupled with that of its dependencies it can limit the operational agility of the application, particularly where new releases are concerned_
## Health Modelling
    
### Resource/Infrastructure Level Monitoring
            
* Is resource-level monitoring enforced throughout the application?


  _Resource- or infrastructure-level monitoring refers to the used platform services such as Azure VMs, Express Route or SQL Database. But also covers 3rd-party solutions like an NVA._
  > All application resources should be configured to route diagnostic logs and metrics to the chosen log aggregation technology. Azure Policy should also be used as a device to ensure the consistent use of diagnostic settings across the application, to enforce the desired configuration for each Azure service.
### Data Interpretation &amp; Health Modelling
            
* Are application level events automatically correlated with resource level metrics to quantify the current application state?


  _The overall health state can be impacted by both application-level issues as well as resource-level failures. Telemetry correlation should be used to ensure transactions can be mapped through the end-to-end application and critical system flows, as this is vital to root cause analysis for failures. Platform-level metrics and logs such as CPU percentage, network in/out, and disk operations/sec should be collected from the application to inform a health model and detect/predict issues([Telemetry correlation](https://docs.microsoft.com/azure/azure-monitor/app/correlation)). This can also help to distinguish between transient and non-transient faults_
* Is a health model used to qualify what 'healthy' and 'unhealthy' states represent for the application?


  > A holistic application health model should be used to quantify what 'healthy' and 'unhealthy' states represent across all application components. It is highly recommended that a 'traffic light' model be used to indicate a green/healthy state when key non-functional requirements and targets are fully satisfied and resources are optimally utilized, e.g. 95% of requests are processed in <= 500ms with AKS node utilization at x% etc. Once established, this health model should inform critical monitoring metrics across system components and operational sub-system composition. It is important to note that the health model should clearly distinguish between expected-transient but recoverable failures and a true disaster state.
    - Is a health model used to qualify what &#39;healthy&#39; and &#39;unhealthy&#39; states represent for the application?



      > A holistic application health model should be used to quantify what 'healthy' and 'unhealthy' states represent across all application components. It is highly recommended that a 'traffic light' model be used to indicate a green/healthy state when key non-functional requirements and targets are fully satisfied and resources are optimally utilized, e.g. 95% of requests are processed in <= 500ms with AKS node utilization at x% etc. Once established, this health model should inform critical monitoring metrics across system components and operational sub-system composition. It is important to note that the health model should clearly distinguish between expected-transient but recoverable failures and a true disaster state.
    - Is a health model used to qualify what &#39;healthy&#39; and &#39;unhealthy&#39; states represent for the application?



      > A holistic application health model should be used to quantify what 'healthy' and 'unhealthy' states represent across all application components. It is highly recommended that a 'traffic light' model be used to indicate a green/healthy state when key non-functional requirements and targets are fully satisfied and resources are optimally utilized, e.g. 95% of requests are processed in <= 500ms with AKS node utilization at x% etc. Once established, this health model should inform critical monitoring metrics across system components and operational sub-system composition. It is important to note that the health model should clearly distinguish between expected-transient but recoverable failures and a true disaster state.
### Alerting
            
* Have Azure Service Health alerts been created to respond to Service-level events?


  _Azure Service Health provides a view into the health of Azure services and regions, as well as issuing service impacting communications about outages, planned maintenance activities, and other health advisories._
  > Azure Service Health Alerts should be configured to operationalize Service Health events, however, Service Health alerts should not be used to detect issues due to associated latencies; there is a 5 minute SLO for automated issues, but many issues require manual interpretation to define an RCA. Instead, they should be used to provide extremely useful information to help interpret issues that have already been detected and surfaced via the health model, to inform how best to operationally respond([Azure Service Health](https://docs.microsoft.com/en-us/azure/service-health/overview))
* Have Azure Resource Health alerts been created to respond to Resource-level events?


  _Azure Resource Health provides information about the health of individual resources such as a specific virtual machine, and is highly useful when diagnosing unavailable resources._
  > Azure Resource Health Alerts should be configured for specific resource groups and resource types, and should be adjusted to maximize signal to noise ratios, i.e. only distribute a notification when a resource becomes unhealthy according to the application health model or due to an Azure platform initiated event. It is therefore important to consider transient issues when setting an appropriate threshold for resource unavailability, such as configuring an alert for a virtual machine with a threshold of 1 minute for unavailability before an alert is triggered
* Do all alerts require an immediate response from an on-call engineer?


  _Alerts only deliver value if they are actionable and effectively prioritized by on-call engineers through defined operational procedures_
### Monitoring and Measurement
            
* Is white-box monitoring used to instrument the application with semantic logs and metrics?


  _Application level metrics and logs, such as current memory consumption or request latency, should be collected from the application to inform a health model and detect/predict issues([Instrumenting an application with Application Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview))_
* Is the application instrumented to measure the customer experience?


  _Effective instrumentation is vital to detecting and resolving performance anomalies that can impact customer experience and application availability([Monitor performance](https://docs.microsoft.com/en-us/azure/azure-monitor/app/web-monitor-performance))_
* Is the application instrumented to track calls to dependent services?


  _Dependency tracking and measuring the duration/status of dependency calls is vital to measuring overall application health and should be used to inform a health model for the application([Dependency Tracking](https://docs.microsoft.com/en-us/azure/azure-monitor/app/asp-net-dependencies))_
* Is black-box monitoring used to measure platform services and the resulting customer experience?


  _Black-box monitoring tests externally visible application behavior without knowledge of the internals of the system. This is a common approach to measuring customer-centric SLIs/SLOs/SLAs([Azure Monitor Reference](https://docs.microsoft.com/en-us/azure/azure-monitor/app/monitor-web-app-availability))_
* Are there known gaps in application observability that led to missed incidents and/or false positives?


  _What you cannot see, you cannot measure. What you cannot measure, you cannot improve_
* Are error budgets used to track service reliability?


  _An error budget describes the maximum amount of time that the application can fail without consequence, and is typically calculated as 1-SLA. For example, if the SLA specifies that the application will function 99.99% of the time before the business has to compensate customers, the error budget is 52 minutes and 35 seconds per year. Error budgets are a device to encourage development teams to minimize real incidents and maximize innovation by taking risks within acceptable limits, given teams are free to ‘spend’ budget appropriately_
* Is there an policy that dictates what will happen when the error budget has been exhausted?


  _If the application error budget has been met or exceeded and the application is operating at or below the defined SLA, a policy may stipulate that all deployments are frozen until they reduce the number of errors to a level that allows deployments to proceed_
## Capacity &amp; Service Availability
    
### Service Availability
            
* Are Azure services available in the required regions?


  _All Azure services and SKUs are not available within every Azure region, so it is important to understand if the selected regions for the application offer all of the required capabilities. Service availability also varies across sovereign clouds, such as China ("Mooncake") or USGov, USNat, and USSec clouds. In situations where capabilities are missing, steps should be taken to ascertain if a roadmap exists to deliver required services([Azure Products by Region](https://azure.microsoft.com/en-us/global-infrastructure/services/))._
* Are Azure Availability Zones available in the required regions?


  _Not all regions support Availability Zones today, so when assessing the suitability of availability strategy in relation to targets it is important to confirm if targeted regions also provide zonal support. All net new Azure regions will conform to the 3 + 0 datacenter design, and where possible existing regions will expand to provide support for Availability Zones([Regions that support Availability Zones in Azure](https://docs.microsoft.com/en-us/azure/availability-zones/az-region))_
* Are any preview services/capabilities required in production?


  _If the application has taken a dependency on preview services or SKUs then it is important to ensure that the level of support and committed SLAs are in alignment with expectations and that roadmap plans for preview services to go 
Generally Available (GA) are understood 
Private Preview : SLAs do not apply and formal support is not generally provided 
Public Preview : SLAs do not apply and formal support may be provided on a best-effort basis_
* Are all APIs/SDKs validated against target runtime/languages for required functionality?


  _While there is a desire across Azure to achieve API/SDK uniformity for supported languages and runtimes, the reality is that capability deltas exist. For instance, not all CosmosDB APIs support the use of direct connect mode over TCP to bypass the platform HTTP gateway. It is therefore important to ensure that APIs/SDKs for selected languages and runtimes provide all of the required capabilities_
### Capacity
            
* Is there a capacity model for the application?


  _A capacity model should describe the relationships between the utilization of various components as a ratio, to capture when and how application components should scale-out. For instance, scaling the number of Application Gateway v2 instances may put excess pressure on downstream components unless also scaled to a degree. When modelling capacity for critical system components it is therefore recommended that an N+1 model be applied to ensure complete tolerance to transient faults, where n describes the capacity required to satisfy performance and availability requirements([Performance Efficiency - Capacity](https://docs.microsoft.com/en-us/azure/architecture/framework/scalability/capacity))_
* Is the required capacity (initial and future growth) within Azure service scale limits and quotas?


  _Due to physical and logical resource constraints within the platform, Azure must apply limits and quotas to service scalability, which may be either hard or soft. The application should therefore take a scale-unit approach to navigate within service limits, and where necessary consider multiple subscriptions which are often the boundary for such limits. It is highly recommended that a structured approach to scale be designed up-front rather than resorting to a 'spill and fill' model([Azure subscription and service limits, quotas, and constraints](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/azure-subscription-service-limits))_
* Is the required capacity (initial and future growth) available within targeted regions?


  _While the promise of the cloud is infinite scale, the reality is that there are finite resources available and as a result situations can occur where capacity can be constrained due to overall demand. If the application requires a large amount of capacity or expects a significant increase in capacity then effort should be invested to ensure that desired capacity is attainable within selected region(s). For applications leveraging a recovery or active-passive based disaster recovery strategy, consideration should also be given to ensure suitable capacity exists in the secondary region(s) since a regional outage can lead to a significant increase in demand within a paired region due to other customer workloads also failing over. To help mitigate this, consideration should be given to pre-provisioning resources within the secondary region([Azure Capacity](https://aka.ms/AzureCapacity))_
## Application Platform Availability
    
### Service SKU
            
* Are all application platform services running in a HA configuration/SKU?


  _Azure application platform services offer resiliency features to support application reliability, though they may only be applicable at a certain SKU. For instance, Service Bus Premium SKU provides predictable latency and throughput to mitigate noisy neighbor scenarios, as well as the ability to automatically scale and replicate metadata to another Service Bus instance for failover purposes([Azure Service Bus Premium SKU](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-premium-messaging)_
### Compute Availability
            
* Is the application platform deployed across multiple regions?


  _The ability to respond to disaster scenarios for overall compute platform availability and application resiliency is dependant on the use of multiple regions or other deployment locations_
    - Is the application platform deployed across multiple regions?


      _The ability to respond to disaster scenarios for overall compute platform availability and application resiliency is dependant on the use of multiple regions or other deployment locations_

* Is the underlying application platform service Availability Zone aware?


  _Platform services that can leverage Availability Zones are deployed in either a zonal manner within a particular zone, or in a zone-redundant configuration across multiple zones([Building solutions for high availability using Availability Zones](https://docs.microsoft.com/en-us/azure/architecture/high-availability/building-solutions-for-high-availability))_
* Is the application hosted across 2 or more application platform nodes?


  _To ensure application platform reliability, it is vital that the application be hosted across at least two nodes to ensure there are no single points of failure. Ideally An n+1 model should be applied for compute availability where n is the number of instances required to support application availability and performance requirements. It is important to note that the higher SLAs provided for virtual machines and associated related platform services, require at least two replica nodes deployed to either an Availability Set or across two or more Availability Zones([SLA for Virtual Machines](https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_9/))_
    - Is the application hosted across 2 or more application platform nodes?


      _To ensure application platform reliability, it is vital that the application be hosted across at least two nodes to ensure there are no single points of failure. Ideally An n+1 model should be applied for compute availability where n is the number of instances required to support application availability and performance requirements. It is important to note that the higher SLAs provided for virtual machines and associated related platform services, require at least two replica nodes deployed to either an Availability Set or across two or more Availability Zones([SLA for Virtual Machines](https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_9/))_

* How is the client traffic routed to the application in the case of region, zone or network outage?


  _In the event of a major outage, client traffic should be routable to application deployments which remain available across other regions or zones. This is ultimately where cross-premises connectivity and global load balancing should be used, depending on whether the application is internal and/or external facing. Services such as Azure Front Door, Azure Traffic Manager, or third-party CDNs can route traffic across regions based on application health solicited via health probes([Traffic Manager endpoint monitoring](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-monitoring))_
## Data Platform Availability
    
### Service SKU
            
* Are all data and storage services running in a HA configuration/SKU?


  _Azure data platform services offer resiliency features to support application reliability, though they may only be applicable at a certain SKU. For instance, Azure SQL Database Business Critical SKUs, or Azure Storage Zone Redundant Storage (ZRS) with three synchronous replicas spread across AZs_
### Consistency
            
* How does CAP theorem apply to the data platform and key application scenarios?


  _CAP theorem proves that it is impossible for a distributed data store to simultaneously provide more than two guarantees across 1) Consistency (every read receives the most recent write or an error), 2) Availability (very request receives a non-error response, without the guarantee that it contains the most recent write), and 3) Partition tolerance (a system continues to operate despite an arbitrary number of transactions being dropped or delayed by the network between nodes). Determining which of these guarantees are most important in the context of application requirements is critical_
* Are data types categorized by data consistency requirements?


  _Data consistency requirements, such as strong or eventual consistency, should be understood for all data types and used to inform data grouping and categorization, as well as what data replication/synchronization strategies can be considered to meet application reliability targets_
### Replication and Redundancy
            
* Is data replicated across paired regions and/or Availability Zones


  _Replicating data across zones or paired regions supports application availability objectives to limit the impact of failure scenarios_
* Is data backed-up to zone-redundant or geo-redundant storage?


  _The ability to restore data from a backup is essential when recovering from data corruption situations as well as failure scenarios. To ensure sufficient redundancy and availability for zonal and regional failure scenarios, such backups should be stored across zones and/or regions_
* Has a data restore process been defined and tested to ensure a consistent application state?


  _Regular testing of the data restore process promotes operational excellence and confidence in the ability to recover data in alignment with defined recovery objectives for the application_
* How is application traffic routed to data sources in the case of region, zone, or network outage?


  _Understanding the method used to route application traffic to data sources in the event of a major failure event is critical to identify whether failover processes will meet recovery objectives. Many Azure data platform services offer native reliability capabilities to handle major failures, such as Cosmos DB Automatic Failover or Azure SQL DB Active Geo-Replication. However, it is important to note that some capabilities such as Azure Storage RA-GRS and Azure SQL DB Active Geo-Replication require application-side failover to alternate endpoints in some failure scenarios, so application logic should be developed to handle these scenarios_
## Networking &amp; Connectivity
    
### Connectivity
            
* Is a global load balancer used to distribute traffic and/or failover across regions?


  _Azure Front Door, Azure Traffic Manager, or third-party CDN services can be used to direct inbound requests to external-facing application endpoints deployed across multiple regions. It is important to note that Traffic Manager is a DNS based load balancer, so failover must wait for DNS propagation to occur. A sufficiently low TTL (Time To Live) value should be used for DNS records, though not all ISPs may honor this. For application scenarios requiring transparent failover, Azure Front Door should be used([Disaster Recovery using Azure Traffic Manager](https://docs.microsoft.com/en-us/azure/networking/disaster-recovery-dns-traffic-manager))([Azure Frontdoor routing architecture](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-routing-architecture))_
* For cross-premises connectivity (ExpressRoute or VPN) are there redundant connections from different locations?


  _At least two redundant connections should be established across two or more Azure regions and peering locations to ensure there are no single points of failure. An active/active load-shared configuration provides path diversity and promotes availability of network connection paths([Cross-network connectivity](https://docs.microsoft.com/azure/expressroute/cross-network-connectivity))_
* Has a failure path been simulated to ensure connectivity is available over alternative paths?


  _The failure of a connection path onto other connection paths should be tested to validate connectivity and operational effectiveness. Using Site-to-Site VPN connectivity as a backup path for ExpressRoute provides an additional layer of network resiliency for cross-premises connectivity([Using site-to-site VPN as a backup for ExpressRoute private peering](https://docs.microsoft.com/azure/expressroute/use-s2s-vpn-as-backup-for-expressroute-privatepeering))_
* Have all single points of failure been eliminated from the data path (on-premises and Azure)?


  _Single-instance Network Virtual Appliances (NVAs), whether deployed in Azure or within an on-premises datacenter, introduce significant connectivity risk([Deploy highly available network virtual appliances](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)_
### Zone-Aware Services
            
* Are ExpressRoute/VPN zone-redundant Virtual Network Gateways used?


  _Zone-redundant virtual network gateways distribute gateway instances across Availability Zones to improve reliability and ensure availability during failure scenarios impacting a datacenter within a region([Zone-redundant Virtual Network Gateways](https://docs.microsoft.com/en-us/azure/vpn-gateway/about-zone-redundant-vnet-gateways))_
* If used, is Azure Application Gateway v2 deployed in a zone-redundant configuration?


  _Azure Application Gateway v2 can be deployed in a zone-redundant configuration to deploy gateway instances across zones for improved reliability and availability during failure scenarios impacting a datacenter within a region([Zone-redundant Application Gateway v2](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-autoscaling-zone-redundant))_
* Is Azure Load Balancer Standard being used to load-balance traffic across Availability Zones?


  _Azure Load Balancer Standard is zone-aware to distribute traffic across Availability Zones and can also be configured in a zone-redundant configuration to improve reliability and ensure availability during failure scenarios impacting a datacenter within a region([Standard Load Balancer and Availability Zones](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-standard-availability-zones))_
* Are health probes configured for Azure Load Balancer(s)/Azure Application Gateway(s)?


  _Health probes allow Azure Load Balancers to assess the health of backend endpoints to prevent traffic from being sent to unhealthy instances([Load Balancer health probes](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview))_
* Do health probes assess critical application dependencies?


  _Custom health probes should be used to assess overall application health including downstream components and dependent services, such as APIs and datastores, so that traffic is not sent to backend instances that cannot successfully process requests due to dependency failures([Health Endpoint Monitoring Pattern](https://docs.microsoft.com/en-us/azure/architecture/patterns/health-endpoint-monitoring))_
## Scalability &amp; Performance
    
### App Performance
            
* Does the application logic handle exceptions and errors using resiliency patterns?


  _Programming paradigms such as retry patterns, request timeouts, and circuit breaker patterns can improve application resiliency by automatically recovering from transient faults([Error handling for resilient applications](https://docs.microsoft.com/en-us/azure/architecture/framework/resiliency/app-design-error-handling))_
* Does the application require long running TCP connections?


  _If an application is initiating many outbound TCP or UDP connections it may exhaust all available ports leading to SNAT port exhaustion and poor application performance. Long-running connections exacerbate this risk by occupying ports for sustained durations. Effort should be taken to ensure that the application can scale within the port limits of the chosen application hosting platform([Managing SNAT port exhaustion](https://docs.microsoft.com/en-us/azure/load-balancer/troubleshoot-outbound-connection#snatexhaust))_
### Data Size/Growth
            
* Are target data sizes and associated growth rates calculated per scenario or service?


  _Scale limits and recovery options should be assessed in the context of target data sizes and growth rates to ensure suitable capacity exists_
* Are there any mitigation plans defined in case data size exceeds limits?


  _Mitigation plans such as purging or archiving data can help the application to remain available in scenarios where data size exceeds expected limits_
### Data Latency and Throughput
            
* Are latency targets defined, tested, and validated for key scenarios?


  _Latency targets, which are commonly defined as first byte in to last byte out, should be defined and measured for key application scenarios, as well as each individual component, to validate overall application performance and health_
* Are throughput targets defined, tested, and validated for key scenarios?


  _Throughput targets, which are commonly defined in terms of IOPS, MB/s and Block Size, should be defined and measured for key application scenarios, as well as each individual component, to validate overall application performance and health. Available throughput typically varies based on SKU, so defined targets should be used to inform the use of appropriate SKUs_
### Network Throughput and Latency
            
* Are there any components/scenarios that are very sensitive to network latency?


  _Components or scenarios that are sensitive to network latency may indicate a need for co-locality within a single Availability Zone or even closer using Proximity Placement Groups with Accelerated Networking enabled([Proximity Placement Groups](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/co-location#proximity-placement-groups))_
* Have gateways (ExpressRoute or VPN) been sized accordingly to the expected cross-premises network throughput?


  _Azure Virtual Network Gateways throughput varies based on SKU. Gateways should therefore be sized according to required throughput([VPN Gateway SKUs](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku))_
* Does the application require dedicated bandwidth?


  _Applications with stringent throughput requirements may require dedicated bandwidth to remove the risks associated with noisy neighbor scenarios_
* If NVAs are used, has expected throughput been tested?


  _Maximum potential throughput for third-party NVA solutions is based on a combination of the leveraged VM SKU size, support for Accelerated Networking, support for HA ports, and more generally the NVA technology used. Expected throughput should be tested to ensure optimal performance, however, it is best to confirm throughput requirements with the NVA vendor directly_
    - If NVAs are used, has expected throughput been tested?


      _Maximum potential throughput for third-party NVA solutions is based on a combination of the leveraged VM SKU size, support for Accelerated Networking, support for HA ports, and more generally the NVA technology used. Expected throughput should be tested to ensure optimal performance, however, it is best to confirm throughput requirements with the NVA vendor directly_

### Elasticity
            
* Can the application scale horizontally in response to changing load?


  _A scale-unit approach should be taken to ensure that each application component and the application as a whole can scale effectively in response to changing demand. A robust capacity model should be used to define when and how the application should scale_
* Has the time to scale in/out been measured?


  _Time to scale-in and scale-out can vary between Azure services and instance sizes and should be assessed to determine if a certain amount of pre-scaling is required to handle scale requirements and expected traffic patterns, such as seasonal load variations_
* Is autoscaling enabled and integrated within Azure Monitor?


  _Autoscaling can be leveraged to address unanticipated peak loads to help prevent application outages caused by overloading_
    - Is autoscaling enabled and integrated within Azure Monitor?


      _Autoscaling can be leveraged to address unanticipated peak loads to help prevent application outages caused by overloading_

## Security &amp; Compliance
    
### Identity and Access
            
* Is the identity provider and associated dependencies highly available?


  _It is important to confirm that the identity provider (e.g. Azure AD, AD, or ADFS) and its dependencies (e.g. DNS and network connectivity to the identity provider) are designed in a way and provide an SLA/SLO that aligns with application availability targets_
* Has role-based and/or resource-based authorization been configured within Azure AD?


  _Role-based and resource-based authorization are common approaches to authorize users based on required permission scopes([Role-based and resource-based authorization](https://docs.microsoft.com/en-us/azure/architecture/multitenant-identity/authorize))_
    - Has role-based and/or resource-based authorization been configured within Azure AD?


      _Role-based and resource-based authorization are common approaches to authorize users based on required permission scopes([Role-based and resource-based authorization](https://docs.microsoft.com/en-us/azure/architecture/multitenant-identity/authorize))_

    - Has role-based and/or resource-based authorization been configured within Azure AD?


      _Role-based and resource-based authorization are common approaches to authorize users based on required permission scopes([Role-based and resource-based authorization](https://docs.microsoft.com/en-us/azure/architecture/multitenant-identity/authorize))_

* Are Azure AD emergency access accounts and processes defined for recovering from identity failures?


  _The impact of no administrative access can be mitigated by creating two or more emergency access accounts([Emergency Access accounts in Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-emergency-access))_
### Security Center
            
* Is Azure Security Center Standard tier enabled for all subscriptions and reporting to centralized workspaces? Also, is automatic provisioning enabled for all subscriptions? ([Security Center Data Collection](https://docs.microsoft.com/en-us/azure/security-center/security-center-enable-data-collection))


* Is Azure Security Center's Secure Score being formally reviewed and improved on a regular basis? ([Security Center Secure Score](https://docs.microsoft.com/en-us/azure/security-center/secure-score-security-controls))


* Are contact details set in security center to the appropriate email distribution list? ([Security Center Contact Details](https://docs.microsoft.com/en-us/azure/security-center/security-center-provide-security-contact-details))


### Network Security
            
* Are all external application endpoints secured?


  _External application endpoints should be protected against common attack vectors, such as Denial of Service (DoS) attacks like Slowloris, to prevent potential application downtime due to malicious intent. Azure native technologies such as Azure Firewall, Application Gateway/Azure Front Door WAF, and DDoS Protection Standard Plan can be used to achieve requisite protection([Azure DDoS Protection](https://docs.microsoft.com/en-us/azure/virtual-network/ddos-protection-overview))_
* Is communication to Azure PaaS services secured using VNet Service Endpoints or Private Link?


  _Service Endpoints and Private Link can be leveraged to restrict access to PaaS endpoints from only authorized virtual networks, effectively mitigating data intrusion risks and associated impact to application availability. Service Endpoints provide service level access to a PaaS service, while Private Link provides direct access to a specific PaaS resource to mitigate data exfiltration risks (e.g. malicious admin scenarios)_
* If data exfiltration concerns exist for services where Private Link is not yet supported, is filtering via Azure Firewall or an NVA being used?


  _NVA solutions and Azure Firewall (for supported protocols) can be leveraged as a reverse proxy to restrict access to only authorized PaaS services for services where Private Link is not yet supported([Azure Firewall](https://docs.microsoft.com/en-us/azure/firewall/features))_
* Are Network Security Groups (NSGs) being used?


  _If NSGs are being used to isolate and protect the application, the rule set should be reviewed to confirm that required services are not unintentionally blocked([Azure Platform Considerations for NSGs](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#azure-platform-considerations))_
    - Are Network Security Groups (NSGs) being used?


      _If NSGs are being used to isolate and protect the application, the rule set should be reviewed to confirm that required services are not unintentionally blocked([Azure Platform Considerations for NSGs](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#azure-platform-considerations))_

## Operational Procedures
    
### Recovery &amp; Failover
            
* Are recovery steps defined for failover and failback?


  _Is there a clearly defined playbook for failover and failback procedures?_
  > The steps required to fail over the application to a secondary Azure region in failure situations should be codified, preferably in an automated manner, to ensure capabilities exist to effectively respond to an outage in a way that limits impact. Similar codified steps should also exist to capture the process required to failback the application to the primary region once a failover triggering issue has been addressed
    - Are recovery steps defined for failover and failback?


      _Is there a clearly defined playbook for failover and failback procedures?_

      > The steps required to fail over the application to a secondary Azure region in failure situations should be codified, preferably in an automated manner, to ensure capabilities exist to effectively respond to an outage in a way that limits impact. Similar codified steps should also exist to capture the process required to failback the application to the primary region once a failover triggering issue has been addressed
    - Are recovery steps defined for failover and failback?


      _Is there a clearly defined playbook for failover and failback procedures?_

      > The steps required to fail over the application to a secondary Azure region in failure situations should be codified, preferably in an automated manner, to ensure capabilities exist to effectively respond to an outage in a way that limits impact. Similar codified steps should also exist to capture the process required to failback the application to the primary region once a failover triggering issue has been addressed
    - Are recovery steps defined for failover and failback?


      _Is there a clearly defined playbook for failover and failback procedures?_

      > The steps required to fail over the application to a secondary Azure region in failure situations should be codified, preferably in an automated manner, to ensure capabilities exist to effectively respond to an outage in a way that limits impact. Similar codified steps should also exist to capture the process required to failback the application to the primary region once a failover triggering issue has been addressed
    - Are recovery steps defined for failover and failback?


      _Is there a clearly defined playbook for failover and failback procedures?_

      > The steps required to fail over the application to a secondary Azure region in failure situations should be codified, preferably in an automated manner, to ensure capabilities exist to effectively respond to an outage in a way that limits impact. Similar codified steps should also exist to capture the process required to failback the application to the primary region once a failover triggering issue has been addressed
* Are automated recovery procedures in place for common failure event?


  _Is there at least some automation for certain failure scenarios or are all those depending on manual intervention?_
  > Automated responses to specific events help to reduce response times and limit errors associated with manual processes. Thus, wherever possible, it is recommended to have automation in place instead of relying on manual intervention.
    - Are automated recovery procedures in place for common failure event?


      _Is there at least some automation for certain failure scenarios or are all those depending on manual intervention?_

      > Automated responses to specific events help to reduce response times and limit errors associated with manual processes. Thus, wherever possible, it is recommended to have automation in place instead of relying on manual intervention.
* Are critical manual processes defined and documented for manual failure responses?


  _While full automation is attainable, there might be cases where manual steps cannot be avoided._
  > Operational runbooks should be defined to codify the procedures and relevant information needed for operations staff to respond to failures and maintain operational health
    - Are critical manual processes defined and documented for manual failure responses?


      _While full automation is attainable, there might be cases where manual steps cannot be avoided._

      > Operational runbooks should be defined to codify the procedures and relevant information needed for operations staff to respond to failures and maintain operational health
### Scalability &amp; Capacity Model
            
* Is the process to provision and deprovision capacity codified?


  > Fluctuation in application traffic is typically expected. To ensure optimal operation is maintained, such variations should be met by automated scalability. The significance of automated capacity responses underpinned by a robust capacity model was highlighted by the COVID-19 crisis where many applications experienced severe traffic variations. While Auto-scaling enables a PaaS or IaaS service to scale within a pre-configured (and often times limited) range of resources, is provisioning or deprovisioning capacity a more advanced and complex process of for example adding additional scale units like additional clusters, instances or deployments. The process should be codified, automated and the effects of adding/removing capacity should be well understood.
* Is capacity utilization monitored and used to forecast future growth?


  _Predicting future growth and capacity demands can prevent outages due to insufficient provisioned capacity over time._
  > Especially when demand is fluctuating, it is useful to monitor historical capacity utilization to derive predictions about future growth. Azure Monitor provides the ability to collect utilization metrics for Azure services so that they can be operationalized in the context of a defined capacity model. The Azure Portal can also be used to inspect current subscription usage and quota status([Supported metrics with Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/metrics-supported))
### Configuration &amp; Secrets Management
            
* Where is application configuration information stored and how does the application access it?


  _Application configuration information can be stored together with the application itself or preferably using a dedicated configuration management system like Azure App Configuration or Azure Key Vault_
  > Preferably configuration information is stored using a dedicated configuration management system like Azure App Configuration or Azure Key Vault so that it can be updated independently of the application code.
* Do you have procedures in place for key/secret rotation?


  _In the situation where a key or secret becomes compromised, it is important to be able to quickly act and generate new versions. Key rotation reduces the attack vectors and should be automated and executed without any human interactions._
  > Secrets (keys, certificates etc.) should be replaced once they have reached the end of their active lifetime or once they have been compromised. Renewed certificates should also use a new key. A process needs to be in place for situations where keys get compromised (leaked) and need to be regenerated on-demand. Tools, such as Azure Key Vault should ideally be used to store and manage application secrets to help with rotation processes([Key Vault Key Rotation](https://docs.microsoft.com/azure/key-vault/secrets/tutorial-rotation-dual))
* Are keys and secrets backed-up to geo-redundant storage?


  _Keys and secrets must still be available in a failover case._
  > Keys and secrets should be backed up to geo-redundant storage so that they can be accessed in the event of a regional failure and support recovery objectives. In the event of a regional outage, the Key Vault service will automatically be failed over to the secondary region in a read-only state([Azure Key Vault availability and reliability](https://docs.microsoft.com/en-us/azure/key-vault/general/disaster-recovery-guidance))
    - Are keys and secrets backed-up to geo-redundant storage?


      _Keys and secrets must still be available in a failover case._

      > Keys and secrets should be backed up to geo-redundant storage so that they can be accessed in the event of a regional failure and support recovery objectives. In the event of a regional outage, the Key Vault service will automatically be failed over to the secondary region in a read-only state([Azure Key Vault availability and reliability](https://docs.microsoft.com/en-us/azure/key-vault/general/disaster-recovery-guidance))
* Is Soft-Delete enabled for Key Vaults and Key Vault objects?


  _The Soft-Delete feature retains resources for a given retention period after a DELETE operation has been performed, while giving the appearance that the object is deleted. It helps to mitigate scenarios where resources are unintentionally, maliciously or incorrectly deleted([Azure Key Vault Soft-Delete](https://docs.microsoft.com/en-us/azure/key-vault/general/overview-soft-delete))_
  > Key Vault Soft Delete helps to mitigate scenarios where resources are unintentionally, maliciously or incorrectly deleted([Azure Key Vault Soft-Delete](https://docs.microsoft.com/en-us/azure/key-vault/general/overview-soft-delete)). It is therefore highly recommended to enable this.
* Is the application stateless or stateful? If it is stateful, is the state externalized in a data store?


  _Stateless processes can easily be hosted across multiple compute instances to meet scale demands, as well as helping to reduce complexity and ensure high cacheability([Stateless web services](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices))_
* Is the session state (if any) non-sticky and externalized to a data store?


  _Sticky session state limits application scalability because it is not possible to balance load. With sticky sessions all requests from a client must be sent to the same compute instance where the session state was initially created, regardless of the load on that compute instance. Externalizing session state allows for traffic to be evenly distributed across multiple compute nodes, with required state retrieved from the external data store([Avoid session state](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#sessionstate))_
## Deployment &amp; Testing
    
### Application Deployments
            
* Can the application be deployed automatically from scratch without any manual operations?


  _Manual deployment steps introduce significant risks where human error is concerned and also increases overall deployment times._
  > Manual deployment steps introduce significant risks where human error is concerned and also increases overall deployment times. Automated end-to-end deployments, with manual approval gates where necessary, should be used to ensure a consistent and efficient deployment process([Deployment considerations for DevOps](https://docs.microsoft.com/en-us/azure/architecture/framework/devops/deployment))
    - Can the application be deployed automatically from scratch without any manual operations?


      _Manual deployment steps introduce significant risks where human error is concerned and also increases overall deployment times._

      > Manual deployment steps introduce significant risks where human error is concerned and also increases overall deployment times. Automated end-to-end deployments, with manual approval gates where necessary, should be used to ensure a consistent and efficient deployment process([Deployment considerations for DevOps](https://docs.microsoft.com/en-us/azure/architecture/framework/devops/deployment))
* How long does it take to deploy an entire production environment?


  _The time it takes for a full deployment needs to algin with recovery targets_
  > The entire end-to-end deployment process should be understood and align with recovery targets
* Can N-1 or N+1 versions be deployed via automated pipelines where N is current deployment version in production?


  _N-1 and N+1 refer to roll-back and roll-forward._
  > Automated deployment pipelines should allow for quick roll-forward and roll-back deployments to address critical bugs and code updates outside of the normal deployment lifecycle
* Does the application deployment process leverage blue-green deployments and/or canary releases?


  _Blue/green or canary deployments are a way to gradually release new feature or changes without impacting all users at once._
  > Blue-green deployments and/or canary releases can be used to deploy updates in a controlled manner that helps to minimize disruption from unanticipated deployment issues([Stage your workloads](https://docs.microsoft.com/en-us/azure/architecture/framework/devops/deployment#stage-your-workloads)) For example, Azure uses canary regions to test and validate new services and capabilities before they are more broadly rolled out to other Azure regions. Where appropriate the application can also use canary environments to validate changes before wider production rollout. Moreover, certain large application platforms may also derive benefit from leveraging Azure canary regions as a basis for validating the potential impact of Azure platform changes on the application.
### Build Environments
            
* Do critical test environments have 1:1 parity with the production environment?


  _Do test environment differ from production in more than just smaller SKUs being used, e.g. by sharing components between different envs?_
  > To completely validate the suitability of application changes, all changes should be tested in an environment that is fully reflective of production, to ensure there is no potential impact from environment deltas
### Testing &amp; Validation
            
* Is the application tested for performance, scalability, and resiliency?


  _Performance Testing: Performance testing is the superset of both load and stress testing. The primary goal of performance testing is to validate benchmark behaviour for the application([Performance Testing](https://docs.microsoft.com/en-us/azure/architecture/checklist/dev-ops#testing))
Load Testing : Load testing validates application scalability by rapidly and/or gradually increasing the load on the application until it reaches a threshold/limit 
Stress Testing : *Stress testing is a type of negative testing which involves various activities to overload existing resources and remove components to understand overall resiliency and how the application responds to issues_
    - Is the application tested for performance, scalability, and resiliency?


      _Performance Testing: Performance testing is the superset of both load and stress testing. The primary goal of performance testing is to validate benchmark behaviour for the application([Performance Testing](https://docs.microsoft.com/en-us/azure/architecture/checklist/dev-ops#testing))
Load Testing : Load testing validates application scalability by rapidly and/or gradually increasing the load on the application until it reaches a threshold/limit 
Stress Testing : *Stress testing is a type of negative testing which involves various activities to overload existing resources and remove components to understand overall resiliency and how the application responds to issues_

    - Is the application tested for performance, scalability, and resiliency?


      _Performance Testing: Performance testing is the superset of both load and stress testing. The primary goal of performance testing is to validate benchmark behaviour for the application([Performance Testing](https://docs.microsoft.com/en-us/azure/architecture/checklist/dev-ops#testing))
Load Testing : Load testing validates application scalability by rapidly and/or gradually increasing the load on the application until it reaches a threshold/limit 
Stress Testing : *Stress testing is a type of negative testing which involves various activities to overload existing resources and remove components to understand overall resiliency and how the application responds to issues_

    - Is the application tested for performance, scalability, and resiliency?


      _Performance Testing: Performance testing is the superset of both load and stress testing. The primary goal of performance testing is to validate benchmark behaviour for the application([Performance Testing](https://docs.microsoft.com/en-us/azure/architecture/checklist/dev-ops#testing))
Load Testing : Load testing validates application scalability by rapidly and/or gradually increasing the load on the application until it reaches a threshold/limit 
Stress Testing : *Stress testing is a type of negative testing which involves various activities to overload existing resources and remove components to understand overall resiliency and how the application responds to issues_

* Are these tests automated and carried out periodically or on-demand?


  _Testing should be fully automated where possible and performed as part of the deployment lifecycle to validate the impact of all application changes. Additionally, manual explorative testing may also be conducted_
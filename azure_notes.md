### Questions
1. Software as a Service(SaaS), Platform as a service (PaaS), Infrastructure as a service (IaaS)

1. Instance types offered by Azure
    - General Purpose - for PoCs, 
    - Compure Optimized - for Gaming, batch processes
    - Memory Optimized - for RDBMs, in-memory analytics, medium to large scale cache
    - Storage Optimized - Big Data, NoSQL, SQL
    - GPU - VMs specialize in heavy graphic rendering & video editing, model training, inferencing with deep learning
    - High Performance Compute - fastest & powerful CPUs.

1. Deployment environments offered by Azure
    * Staging
        - provides a platform to validate changes to your app before it is live in production
        - app can be identified using GUID in URL form GUID.cloudapp.net 
    * Production
        - DNS friendly URL 

1. Advantages of scaling in Azure
    - Autoscaling helps to deal with changing demands in Cloud services, Mobile services, VMs.. 

1. Diff - Windows Active Directory & Azure Active Directory
    - Windows AD - facilitates working with interconnected, complex and different network resources in an unified manner. Emphasis on on-premises units like file services, printers
    - Azure AD is multi-tenant cloud based directory & identity management service. Emphasizes on web based services that use RESTful interfaces

1. Queues offered by Azure
    * Storage Queue 
        - provides messaging within & between services
        - PUB-SUB model
    * Service Bus Queeus
        - Integrates application components that span multiple communication protocols, network enviroments
        - Provides FIFO style of delivery

1. Advantages of Azure Resource Manager (ARM)
    - enables users to manage their usage of application resources

1. How has integrating Hybrid Cloud been useful for Azure?
    - boost productivity by using Azure & Azure stack for building & deploying applications for cloud & on-premises applications
    - combining public & private clouds
    - enables to deploy applications regardless of its location, the cloud or on-premises

1. What is Federation in Azure SQL?
    - SQL Azure Federation provides tools that can enable developers to access or share databases among themselves in SQL Azure
    - Reduces the possibility of a single point of failure - sharding of dbs

1.  Types of storage offered by Azure
        * Blob - unstructured massive data like pictures, music, video files,along with their metadata
        Blob storage places data into different tiers based on how often they are accessed
        * Table - semi-strucutured datasets & a NoSQL key-value store 
        * File - provides file sharing capabilities accessible by the SMB(Server Message Block) protocol. The data is protected by SMB 3.0 & HTTPS
        * Queue - provides message queuing for large workloads in a simple, cost-effective and durable manner.  Ensures the application is scalable & less prone to individual components failing

1. Text Analysis API in Azure Machine Learning
    - set of webservices that can be used for text analysis
    - used to analyze unstructured text for sentiment analysis & key phrase extraction. 0 - negative sentiment, 1  - positive sentiment
    - 120 languages are supported

1. Two kinds of Azure Web Service roles
    - A cloud service role is a set of managed & load-balanced virtual machines that work together to perform tasks
    * Web Roles - to run web apps developed in programming languages supported by IIS like PHP, ASP.NET
    Automatically deploys & hosts applications through the users IIS
    * Worker Roles - peforms supporting background tasks along with web roles. It doesn't use IIS & runs user application standalone

1. Azure Service Fabric - provides a platform that makes the process of developing microservices and managing application lifecycle easier

1. Azure VNET
    -  A client wants the front-end of his app to be hosted on azure but wants the database to be hosted on-premises
        * The ideal solution for this scenario is to use Azure VNET based "Point to Site". Since there are only a limited number of resources that need to be connected
    

1. Azure Traffic Manager
    - is a DNS based traffic load balancer that enables users to provide high availability & responsiveness by distributing traffic in an optimal manner across global Azure regions
    - Enables users to know where customers are connecting from

1.  Group of servers connected in a virtual network. How to move them?
    - You need to isolate network traffic among VMs in a subnet, which is part of a virtual network with little downtime & impact on users.
    * Create a new Virtual Network
    * Move all the VMs in the subnet to the new VN
    - This would ensure that the VMs are kept isolated without the need for additional security like a Network Security Group


1. Clouds 
    * Public - Every component that the user is using is his application are running only on Azure
    * Private - Azure services are being run within an on-premises data center or on-premises data centers are used by the user to host systems or applications
    * Hybrid - Some run on Azure & some on on-premises datacenter

1. How to set up Azure Virtual Machine 
    1. login to Azure
    1. select "create a resource"
    1. select "server(windows/ubuntu)"
    1. enter relevant info
    1. select the size of vm
    1. review & create

1. Azure Virtual Network enables Azure resources to communicate with each other, the internet or on-premises networks securely

1. How To ensure the user is not asked for password everytime he logs in as part of authentication
    * Configure Azure AD sync to use single sign-on

1. How to ensure that VMs remain available while migrating to Azure
    * Express Route
1.  How to validate & deploy changes by a dev team with minimum downtime?
    * Create a staging environment for the site

1. How to handle Standard tier application is loading slowly.  
    * Configure Azure CDN to cache site images & content (static) stored in Azure blob storage





### References
1. https://www.youtube.com/watch?v=_Pyityj08vU
1. https://www.youtube.com/watch?v=PrZijM3PRDw
1. https://www.youtube.com/watch?v=3gnLwSI4d9E

### Azure CLI 
* Create a Resource Group
    - $ ` az group create --name IOT5g360ResourceGroup --location southindia `
    - $ ` az group list --query "[?name == 'IOT5g360ResourceGroup']" `
        ~~~json
        [
            {
                "id": "/subscriptions/109645b2-cbd0-449b-a07d-f09169bcba66/resourceGroups/IOT5g360ResourceGroup",
                "location": "southindia",
                "managedBy": null,
                "name": "IOT5g360ResourceGroup",
                "properties": {
                "provisioningState": "Succeeded"
                },
                "tags": {
                "Name": "Inseego"
                },
                "type": null
            }
        ]
        ~~~
    - $ ` az group list --output table `
        >     Name                              Location      Status
            --------------------------------  ------------  ---------
            appsvc_linux_centralus            centralus     Succeeded
            cloud-shell-storage-centralindia  centralindia  Succeeded
            IOT5g360ResourceGroup             southindia    Succeeded

* Set Azure Subscription 
    - $ ` az account set --subscription mySubscriptionId `
        - `Subscription ID`: 109645b2-cbd0-449b-a07d-f09169bcba66

* Create Storage account
    - $ ` az storage account create --name storageaccountiot5g360 --kind StorageV2 --sku Standard_LRS -l southindia -g IOT5g360ResourceGroup `
        
* Create Azure Media Services(AMS)
    - Create a Resource Group -  $ ` az group create --name IOT5g360ResourceGroup --location southindia `
    - Create a Storage account - $ ` az storage account create --name storageaccountiot5g360 --kind StorageV2 --sku Standard_LRS -l southindia -g IOT5g360ResourceGroup `
    - Create AMS Account - $ ` az ams account create --name amsiot5g360 -l southindia -g IOT5g360ResourceGroup --storage-account storageaccountiot5g360 `

*  Acess Media Services
    - $ ` az ams account sp create --account-name amsiot5g360 --resource-group IOT5g360ResourceGroup `
        - ~~~json
            { //  Retrying role assignment creation: 4/36
                "AadClientId": "ced8d263-4e63-4875-99e7-53291b139683",
                "AadEndpoint": "https://login.microsoftonline.com",
                "AadSecret": "059d41c9-4c1d-4244-9ee1-536216640177",
                "AadTenantId": "4984ed6c-2b33-4532-b600-4eceb5b87a74",
                "AccountName": "amsiot5g360",
                "ArmAadAudience": "https://management.core.windows.net/",
                "ArmEndpoint": "https://management.azure.com/",
                "Region": "South India",
                "ResourceGroup": "IOT5g360ResourceGroup",
                "SubscriptionId": "109645b2-cbd0-449b-a07d-f09169bcba66"
            }
          ~~~ 
    - $ ` az ams account show -n amsiot5g360 -g IOT5g360ResourceGroup `          
           -    ~~~json
                {
                    "id": "/subscriptions/109645b2-cbd0-449b-a07d-f09169bcba66/resourceGroups/IOT5g360ResourceGroup/providers/Microsoft.Media/mediaservices/amsiot5g360",
                    "location": "South India",
                    "mediaServiceId": "170fdf7b-3f08-49c1-bae9-0172b17c4753",
                    "name": "amsiot5g360",
                    "resourceGroup": "IOT5g360ResourceGroup",
                    "storageAccounts": [
                        {
                        "id": "/subscriptions/109645b2-cbd0-449b-a07d-f09169bcba66/resourceGroups/IOT5g360ResourceGroup/providers/Microsoft.Storage/storageAccounts/storageaccountiot5g360",
                        "resourceGroup": "IOT5g360ResourceGroup",
                        "type": "Primary"
                        }
                    ],
                    "tags": null,
                    "type": "Microsoft.Media/mediaservices"
                }
                ~~~
          
* Start the default Streaming Endpoint 
    * The Streaming Endpoint from which you want to stream content has to be in the Running state. 
      The following CLI command starts your default Streaming Endpoint:
      - $ ` az ams streaming-endpoint start -n default -a amsiot5g360 -g IOT5g360ResourceGroup `

* Get Azure AD Token for Service Principal Authentication
    - sent via postman request - ` POST https://login.microsoftonline.com/:tenantId/oauth2/token ` 
        response:
        ~~~json
        {
            "token_type": "Bearer",
            "expires_in": "3599",
            "ext_expires_in": "3599",
            "expires_on": "1568015669",
            "not_before": "1568011769",
            "resource": "https://management.core.windows.net/",
            "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImllX3FXQ1hoWHh0MXpJRXN1NGM3YWNRVkduNCIsImtpZCI6ImllX3FXQ1hoWHh0MXpJRXN1NGM3YWNRVkduNCJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuY29yZS53aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80OTg0ZWQ2Yy0yYjMzLTQ1MzItYjYwMC00ZWNlYjViODdhNzQvIiwiaWF0IjoxNTY4MDE3NjgwLCJuYmYiOjE1NjgwMTc2ODAsImV4cCI6MTU2ODAyMTU4MCwiYWlvIjoiNDJGZ1lIaHFPK212bXIvdnkxUFdyM3RGZTk0ekFnQT0iLCJhcHBpZCI6ImNlZDhkMjYzLTRlNjMtNDg3NS05OWU3LTUzMjkxYjEzOTY4MyIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzQ5ODRlZDZjLTJiMzMtNDUzMi1iNjAwLTRlY2ViNWI4N2E3NC8iLCJvaWQiOiI2YjM1OGUxMy04ZGE1LTQ2OWItODI3YS0wNjkwZDY2ODYxYTAiLCJzdWIiOiI2YjM1OGUxMy04ZGE1LTQ2OWItODI3YS0wNjkwZDY2ODYxYTAiLCJ0aWQiOiI0OTg0ZWQ2Yy0yYjMzLTQ1MzItYjYwMC00ZWNlYjViODdhNzQiLCJ1dGkiOiI3ZGRBQVhwOXdrU0VRaWZJbjc3a0FRIiwidmVyIjoiMS4wIn0.n01yGZ4huS_m6nA0Vpt5n8fp5-Q7c7dX2WOXaeqbzWE5hMKx-YHRSr2Sdd5Ciq6IYmn9kJ09JP6Z67Hdw_XiKs_g4YmeeNyliuAOZiNSzs3Ee8Ztfb-RCLpC6-nL8uMpmhQQL8dV8HSogZI10tL7v4-H21YkRkFgbvdS30jiIaxRDbqul_yJgh4VcWbH6Uh2bAS8shfdma9ppbbj5cnyhZSi2V0HbpWNJyI87EqcyQGdXK_OFuW7cXaWhQdSbNcFXktwZ4-4F-kSqXOghkQ19k0ECzta2tcnXU-VAbIU1th9M8Um25D6sDh07Qn8vwniiTmDBgXWuVaQvobEAKf_og"
        }
        ~~~

* Create or update an Asset 
    * sent via postman request - ` PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}} `
    with the following request body
    ~~~json
        {
        "properties": {
            "description": "Inseego Output Asset",
            "alternateId": "(Optional) some GUID",
            "storageAccountName": "storageaccountiot5g360",
            "container": "inseego-output-asset-1"
        }
        }
    ~~~

    response:
    ~~~json
        {
        "name": "testAsset1",
        "id": "/subscriptions/109645b2-cbd0-449b-a07d-f09169bcba66/resourceGroups/IOT5g360ResourceGroup/providers/Microsoft.Media/mediaservices/amsiot5g360/assets/testAsset1",
        "type": "Microsoft.Media/mediaservices/assets",
        "properties": {
            "assetId": "53d127f2-dd93-41e5-a707-218803c2252d",
            "created": "2019-09-09T07:15:04.313Z",
            "lastModified": "2019-09-09T07:15:04.313Z",
            "alternateId": "(Optional) some GUID",
            "description": "Inseego Output Asset",
            "container": "inseego-output-asset-1",
            "storageAccountName": "storageaccountiot5g360",
            "storageEncryptionFormat": "None"
        }
        }
    ~~~

* List the Asset URLs
    - List all the assets whose names start with the string 'Something'.
        - $ ` az ams asset list -a amsiot5g360 -g IOT5g360ResourceGroup `
                > --query [?starts_with(name,'Something')] `

    -   Postman Query - `POST https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName/listContainerSas?api-version={{api-version}} `
    request body
    ~~~json
        {
            "permissions": "ReadWrite",
            "expiryTime": "2018-01-01T10:00:00.007Z"
        }
    ~~~
    Response:
    ~~~json
        {
            "assetContainerSasUrls": [
                "https://storageaccountiot5g360.blob.core.windows.net/inseego-output-asset-1?sv=2017-04-17&sr=c&sig=1VQQQNIle34AXIAbBmK6NPW0tlpdfrXPENY6VmmH59I%3D&se=2018-01-01T10:00:00Z&sp=rwl",
                "https://storageaccountiot5g360.blob.core.windows.net/inseego-output-asset-1?sv=2017-04-17&sr=c&sig=NhAJFflltaU9K3YYNnDOjdjvVb7zEm8nKs5YVyiy4H8%3D&se=2018-01-01T10:00:00Z&sp=rwl"
            ]
       }
    ~~~

* Create or Update a transform 
    - Postman request - `PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}`
        request body:
        - ~~~json
                {
                    "properties": {
                        "description": "Basic Transform using an Adaptive Streaming encoding preset from the libray of built-in Standard Encoder presets",
                        "outputs": [
                            {
                                "onError": "StopProcessingJob",
                                "relativePriority": "Normal",
                                "preset": {
                                    "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                                    "presetName": "AdaptiveStreaming"
                                }
                            }
                        ]
                    }
                }
        ~~~
    response: 
    ~~~json
    {
        "name": "testTransform1",
        "id": "/subscriptions/109645b2-cbd0-449b-a07d-f09169bcba66/resourceGroups/IOT5g360ResourceGroup/providers/Microsoft.Media/mediaservices/amsiot5g360/transforms/testTransform1",
        "type": "Microsoft.Media/mediaservices/transforms",
        "properties": {
            "created": "2019-09-09T08:39:54.0554775Z",
            "description": "Basic Transform using an Adaptive Streaming encoding preset from the libray of built-in Standard Encoder presets",
            "lastModified": "2019-09-09T08:39:54.0554775Z",
            "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                "presetName": "AdaptiveStreaming"
                }
            }
            ]
        }
    }
    ~~~




* Create Job - Local Media File
    - Postman request ` POST https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}} `
    Request body
    -    ~~~json    
            {
                "properties": {
                    "input": {
                    "@odata.type": "#Microsoft.Media.JobInputAsset",
                    "assetName": "testAssetInput1"
                    },
                    "outputs": [
                    {
                        "@odata.type": "#Microsoft.Media.JobOutputAsset",
                        "assetName": "testAsset1"
                    }
                    ],
                    "priority" : "Normal",
                    "correlationData": {
                        "myKey1" :"my custom correlation metadata for this job"
                    }
                }
            }
        ~~~
    Response
     ~~~json
            {
            "name": "testJob1",
            "id": "/subscriptions/109645b2-cbd0-449b-a07d-f09169bcba66/resourceGroups/IOT5g360ResourceGroup/providers/Microsoft.Media/mediaservices/amsiot5g360/transforms/testTransform1/jobs/testJob1",
            "type": "Microsoft.Media/mediaservices/transforms/jobs",
            "properties": {
                "created": "2019-09-09T09:09:04.3132627Z",
                "state": "Queued",
                "input": {
                "@odata.type": "#Microsoft.Media.JobInputAsset",
                "files": [],
                "assetName": "testAssetInput1"
                },
                "lastModified": "2019-09-09T09:09:04.3132627Z",
                "outputs": [
                {
                    "@odata.type": "#Microsoft.Media.JobOutputAsset",
                    "state": "Queued",
                    "progress": 0,
                    "label": "BuiltInStandardEncoderPreset_0",
                    "assetName": "testAsset1"
                }
                ],
                "priority": "Normal",
                "correlationData": {
                "myKey1": "my custom correlation metadata for this job"
                }
            }
        }
    ~~~          



* Create a Streaming Policy (clear)
  - Postman query - `POST https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingPolicies/:streamingPolicyName?api-version={{api-version}}`  
    ~~~json
        {
            "properties": {
                "noEncryption": {
                "enabledProtocols": {
                    "download": true,
                    "dash": true,
                    "hls": true,
                    "smoothStreaming": true
                }
                }
            }
        }
    ~~~
    Response: 
    ~~~json
        {
            "name": "testStreamingPolicy1",
            "id": "/subscriptions/109645b2-cbd0-449b-a07d-f09169bcba66/resourceGroups/IOT5g360ResourceGroup/providers/Microsoft.Media/mediaservices/amsiot5g360/streamingPolicies/testStreamingPolicy1",
            "type": "Microsoft.Media/mediaservices/streamingPolicies",
            "properties": {
                "created": "2019-09-09T09:17:21.6895424Z",
                "noEncryption": {
                "enabledProtocols": {
                    "download": true,
                    "dash": true,
                    "hls": true,
                    "smoothStreaming": true
                    }
                }
            }
        }



* Miscellaneous
    - $ ` az appservice plan list `
        ~~~json
            [
                {
                    "freeOfferExpirationTime": null,
                    "hostingEnvironmentProfile": null,
                    "hyperV": false,
                    "id": "/subscriptions/109645b2-cbd0-449b-a07d-f09169bcba66/resourceGroups/appsvc_linux_centralus/providers/Microsoft.Web/serverfarms/appsvc_linux_centralus",
                    "isSpot": false,
                    "isXenon": false,
                    "kind": "linux",
                    "location": "Central US",
                    "maximumElasticWorkerCount": 1,
                    "maximumNumberOfWorkers": 1,
                    "name": "appsvc_linux_centralus",
                    "numberOfSites": 1,
                    "perSiteScaling": false,
                    "provisioningState": null,
                    "reserved": true,
                    "resourceGroup": "appsvc_linux_centralus",
                    "sku": {
                    "capabilities": null,
                    "capacity": 1,
                    "family": "F",
                    "locations": null,
                    "name": "F1",
                    "size": "F1",
                    "skuCapacity": null,
                    "tier": "Free"
                    },
                    "spotExpirationTime": null,
                    "status": "Ready",
                    "tags": null,
                    "targetWorkerCount": 0,
                    "targetWorkerSizeId": 0,
                    "type": "Microsoft.Web/serverfarms",
                    "workerTierName": null
                }
            ]
        ~~~
    - $ ` az login`
        ~~~json
        
        [
            {
                "cloudName": "AzureCloud",
                "id": "109645b2-cbd0-449b-a07d-f09169bcba66",
                "isDefault": true,
                "name": "Free Trial",
                "state": "Enabled",
                "tenantId": "4984ed6c-2b33-4532-b600-4eceb5b87a74",
                "user": {
                "name": "kishored@ispace.com",
                "type": "user"
                }
            }
        ]
        ~~~
    - $ 


----
### ToDo Tasks

* Complete deployment design and architecture 
    - VPC creation, resources creation, server application clustering etc.
    - Deployment design/architecture on Azure
    - Azure Infrastructure creation
    - Server application clustering"

*  Samples & tutorials


    - Deploy a sample node-app on azure 
        * https://my-express-app-23aug2019.azurewebsites.net
        * sample expressApp - https://code.visualstudio.com/docs/nodejs/nodejs-tutorial
        * https://code.visualstudio.com/tutorials/app-service-extension/getting-started
        * https://code.visualstudio.com/docs/azure/deployment

    
    * Quickstart: Control a device connected to an IoT hub (Node.js)
            - hostname: IOTHub5g360.azure-devices.net
            - Subscription ID: 109645b2-cbd0-449b-a07d-f09169bcba66
    
        ~~~json
        kishore@Azure:~$ az iot hub device-identity create --hub-name IOTHub5g360 --device-id MyNodeDevice
            {
            "authentication": {
                "symmetricKey": {
                "primaryKey": "zxL+C9uPwGf3Z0sWxh6d5+uxK1Cp1SOqoUpVDXS6U0k=",
                "secondaryKey": "42yO/VAQXlueG/TEsBlICNa10Ep5lCHPZuLtki1mS8k="
                },
                "type": "sas",
                "x509Thumbprint": {
                "primaryThumbprint": null,
                "secondaryThumbprint": null
                }
            },
            "capabilities": {
                "iotEdge": false
            },
            "cloudToDeviceMessageCount": 0,
            "connectionState": "Disconnected",
            "connectionStateUpdatedTime": "0001-01-01T00:00:00",
            "deviceId": "MyNodeDevice",
            "deviceScope": null,
            "etag": "ODg1NTI5ODMz",
            "generationId": "637025702479770574",
            "lastActivityTime": "0001-01-01T00:00:00",
            "status": "enabled",
            "statusReason": null,
            "statusUpdatedTime": "0001-01-01T00:00:00"
            }
        ~~~
    `device connection string`: 
    > kishore@Azure:~$ az iot hub device-identity show-connection-string --hub-name IOTHub5g360 --device-id MyNodeDevice --output table
            ConnectionString
            -------------------------------------------------------------------------------------------------------------------------
            HostName=IOTHub5g360.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey=zxL+C9uPwGf3Z0sWxh6d5+uxK1Cp1SOqoUpVDXS6U0k=
        
    `service connection string `
    > kishore@Azure:~$ az iot hub show-connection-string --name IOTHub5g360 --policy-name service --output table
            ConnectionString
            -------------------------------------------------------------------------------------------------------------------------------
            HostName=IOTHub5g360.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey=uySS+yiYoGbLm/J8rQKtNoCnLM6xoW1IQ45Jeh/kFuo=

    ~~~sh
    @ ~/coding/azure_coding/azure-iot-samples-node/iot-hub/Quickstarts/simulated-device-2 | git:master ✘
        ➜  node SimulatedDevice.js                                                                                                                                                                                
        Sending message: {"temperature":30.056227874135665,"humidity":68.48138143747235}
        message sent
        Sending message: {"temperature":32.29788868011792,"humidity":70.16255415915099}
        message sent
        Sending message: {"temperature":24.835825010649607,"humidity":78.66246430261347}
        message sent
        Sending message: {"temperature":33.078712663899566,"humidity":64.07044705219538}
        message sent
        Sending message: {"temperature":30.691880763185765,"humidity":75.09512004276951}
        message sent
        Sending message: {"temperature":34.786337149873134,"humidity":78.08923326123627}
        message sent
        Sending message: {"temperature":20.34144180493631,"humidity":63.413750932181}
        message sent
        Sending message: {"temperature":27.32482365883846,"humidity":77.35100134805336}
        message sent
        Sending message: {"temperature":29.301871781760113,"humidity":64.5639044646356}
        message sent
        Sending message: {"temperature":25.636384694698684,"humidity":77.63236104210961}
        message sent
    
   
    @ ~/coding/azure_coding/azure-iot-samples-node/iot-hub/Quickstarts/back-end-application | git:master ✘
        ➜  node BackEndApplication.js
        
            Response from SetTelemetryInterval on MyNodeDevice:
            {
            "status": 200,
            "payload": "Telemetry interval set: 10"
            }        
    ~~~
    - After you run the back-end application, you see a message in the console window running the simulated device, and the rate at which it sends messages changes
        > Direct method payload received:
            10
            Response to method 'SetTelemetryInterval' sent successfully.
            message sent

1. https://docs.microsoft.com/en-us/azure/iot-hub/quickstart-control-device-node?toc=/azure/javascript/toc.json&bc=/azure/javascript/breadcrumb/toc.json    
1. https://docs.microsoft.com/en-us/azure/iot-hub/about-iot-hub

-----
### Questions
1. Software as a Service(SaaS), Platform as a service (PaaS), Infrastructure as a service (IaaS)
    - IaaS provides instant computing infrastructure that you can provision and manage over the Internet.
    - PaaS provides ready-made development and deployment environments that you can use to deliver your own cloud services.
    - SaaS delivers applications over the Internet as a web-based service.
    
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

-----
### Azure Media Services references

1. https://docs.microsoft.com/en-us/azure/media-services/latest/media-rest-apis-with-postman
1. https://docs.microsoft.com/en-us/azure/media-services/latest/media-services-apis-overview
1. https://docs.microsoft.com/en-us/azure/media-services/latest/configure-connect-nodejs-howto
1. https://github.com/Azure-Samples/media-services-v3-node-tutorials
1. https://docs.microsoft.com/en-us/azure/media-services/previous/media-services-rest-how-to-use
1. overview: https://docs.microsoft.com/en-us/azure/media-services/latest/media-services-overview

### Azure CLI references
1. Azure Cli - https://docs.microsoft.com/en-us/learn/modules/control-azure-services-with-cli/4-work-with-the-cli
1. https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
1. https://docs.microsoft.com/en-us/azure/media-services/latest/stream-files-cli-quickstart
1. install azure-cli on mac - https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-macos?view=azure-cli-latest

### Azure references
1. https://www.youtube.com/watch?v=_Pyityj08vU
1. https://www.youtube.com/watch?v=PrZijM3PRDw
1. https://www.youtube.com/watch?v=3gnLwSI4d9E
1. https://docs.microsoft.com/en-us/azure/javascript/?view=azure-node-latest


1. https://docs.microsoft.com/en-us/learn/modules/deploy-run-container-app-service/
1. https://docs.microsoft.com/en-us/learn/modules/run-docker-with-azure-container-instances/
1. https://docs.microsoft.com/en-us/learn/paths/administer-containers-in-azure/

1. https://docs.microsoft.com/en-us/azure/architecture/guide/design-principles/index
1. https://docs.microsoft.com/en-us/azure/architecture/guide/pillars
1. https://docs.microsoft.com/en-us/learn/modules/pillars-of-a-great-azure-architecture/
1. https://docs.microsoft.com/en-us/learn/modules/principles-cloud-computing/
1. https://docs.microsoft.com/en-us/azure/architecture/patterns/


#### Microservices
1. https://microservices.io/index.html
1. API gateway 
    - https://www.nginx.com/learn/api-gateway/
    - https://microservices.io/patterns/apigateway.html
    - Kong API - https://medium.com/@far3ns/kong-the-microservice-api-gateway-526c4ca0cfa6
1. https://docs.microsoft.com/en-us/azure/architecture/microservices/design/

------
---
title: Build sustainable applications using Azure services (CDN) 🌱
author: Hacene KASDI
published_date: 2021-11-23
description: One of the main philosophies of Green Software Engineering is that everyone has a part to play in the climate solution, so our decisions have a meaningful impact on the carbon pollution of our applications.
tags: Sustainable applications, Green Software Engineering, Cloud computing, Azure, IaC
---

According to The Green Software Foundation, '`Green software is software that is responsible for emitting fewer greenhouse gases. Our focus is reduction, not neutralization.`'

My name is Hacene KASDI, I’m a fullstack software engineer and Microsoft Azure enthusiast. Currently, consultant at [`@LeLion_Vert`](https://twitter.com/LeLion_Vert) for La Société Générale account, I work on cloud topics and software craft subjects.

At [`@LeLion_Vert`](https://twitter.com/LeLion_Vert), we care about the code quality by reducing technical debt, applying the software craft best practices and developing carbon-aware applications 🌱

During this article, we will browse some green principles and contribute during a software developer journey, even by some small actions.

In this first chapter (suggestion This is the first of a series of article when we will .. because it sounds weird to use the term chapter here), we will apply some best practices to reduce and optimize resource usage, the following is an example that we can apply during software designing or during the `architecture decision records` meeting ([`ADR`](https://adr.github.io/)).


## Rant of a (Green) Software engineer !

Sometimes, as software engineers, we make decisions without thinking about the consequences (consequences on what?), we design and choose our development tools without a deep reflection considering the sustainability of the solution. 

![seedling](/images/2021/11/sustainable-applications/cover.jpeg)

One of the main philosophies of Green Software Engineering (if the term is known, you could add a reference) is that everyone has a part to play in the climate solution, so our decisions have a meaningful impact on the carbon pollution of our applications.

Here the title Technical context is not clear. Are you introducing an example as an application with a given configuration? If yes, maybe you have to modify the title, up to you
**Technical context:** Our application will be used around the world, It will be deployed on one location which is `East US` and the user’s requests around the world should be routed to this point and get the response from the Server.


### Let's consider some solutions before endorsing sustainable principles :

#### Part 1: Using Azure Virtual Machine

We have to deploy an application on an Azure cloud provider, the solution that I will choose here is an Azure Virtual Machine (`Infrastructure as a Service - IaaS`)

An IaaS provides all the infrastructure to support web apps, including storage, web and application servers, and networking resources. We can quickly deploy web apps on IaaS and easily scale infrastructure up and down when demand for the apps is unpredictable.

![VMAzure](/images/2021/11/sustainable-applications/createVirtualMachine.png)

#### Part 2: Using Azure PaaS

But we can improve our solution by reducing the size of this virtual machine to increase utilization. If we want to go further, `Platform as a Service` ([`PaaS`](https://azure.microsoft.com/en-us/overview/what-is-paas/)) solutions are sized more appropriately for their workload and can run those workloads at a high utilization on their underlying compute resources.

I will choose Azure App Services to deploy the docker images hosted on the `ACR (Azure Container Registry)`, as a best practice, we will use the Infrastructure As Code approach for scripting, developing, changing and versioning infrastructure safely and efficiently.

We are going to provision our Cloud services using `Terraform`, we will get `an immutable infrastructure`, a paradigm in which servers are never modified after they’re deployed.

If something needs to be updated, fixed, or modified in any way, new servers are provisioned with a preconfigured image and old servers are destroyed. ([see more about terraform and Infrastructure as code on the official documentaion of HashiCorp](https://www.terraform.io/intro/index.html)).

Below is the first phase of provisioning an Azure App Service, which picks a docker image hosted on the Azure Container Registry. ([`Full solution hosted on GitHub`](https://github.com/kasdihacene/green-software-engineering))


- **Initialize terraform environment**

We need to export some environment variables before running terraform script, these Env Variables will be used to initialize the authentication service principal for Azure provider.

🔷 [Some help - Set Env Variables](https://www.terraform.io/docs/configuration-0-11/variables.html#environment-variables)


```shell
export TF_VAR_tenant=<TENANT>
export TF_VAR_subscription=<SUBS>
export TF_VAR_client_id=<CLIENT ID>
export TF_VAR_client_secret=<SECRET>
```

- **Terraform useful commands**

        terrafom init          Prepare your working directory for other commands

        terrafom validate      Check whether the configuration is valid

        terrafom plan          Show changes required by the current configuration

        terrafom apply         Create or update infrastructure

        terrafom destroy       Destroy previously-created infrastructure


In a nutshell, we will use the HCL syntax to specify the azure cloud provider and the elements that make up our cloud infrastructure. After we create our configuration files for provisioning the resources, we will create an execution plan using the command `terraform plan` that allows us to preview the infrastructure changes before they're deployed. Once the changes verified, we apply the execution plan to deploy the infrastructure.


```hcl
provider "azurerm" {
  tenant_id       = var.tenant
  subscription_id = var.subscription
  client_id       = var.client_id
  client_secret   = var.client_secret
  features {} #This is required for v2 of the provider even if empty or plan will fail
}

## Authenticating using a Service Principal with a Client Secret

variable "tenant" {
  type = string
}
variable "subscription" {
  type = string
}
variable "client_id" {
  type = string
}
variable "client_secret" {
  type = string
}

```

We will specify the location and the repository of the docker image already hosted on the Azure Container Registry. Beforehand, authenticating to the ACR should be performed before provisioning the App Service resource (see more on the terraform below, section >Setup connection with azure container registry for image pulling).


```hcl

resource "azurerm_resource_group" "rg_green_front_fr_001" {
  name     = "rg-green-front-${local.environment_tag}-fr-001"
  location = local.location
}

## -----------------------------------------------------------------------------##
# App service plan configuration to deploy/run docker images pushed to the
# ACR (Azure Container Registry)
## -----------------------------------------------------------------------------##

resource "azurerm_app_service_plan" "greenAppServicePlan" {
  name                = "appservice-plan-green-front-${local.environment_tag}-fr-001"
  location            = azurerm_resource_group.rg_green_front_fr_001.location
  resource_group_name = azurerm_resource_group.rg_green_front_fr_001.name
  kind                = "Linux"
  reserved            = true

  sku {
    tier = "Standard"
    size = "S1"
  }

  tags = {
    environment = local.environment_tag
    application = local.application_tag
  }
}

resource "azurerm_app_service" "greenAppService" {
  name                = "green-front-${local.environment_tag}-fr-001"
  location            = azurerm_resource_group.rg_green_front_fr_001.location
  resource_group_name = azurerm_resource_group.rg_green_front_fr_001.name
  app_service_plan_id = azurerm_app_service_plan.greenAppServicePlan.id

  app_settings = {
    WEBSITES_ENABLE_APP_SERVICE_STORAGE = false

    # Settings for private Container Registries
    DOCKER_REGISTRY_SERVER_URL      = "https://${local.container_registry_login_server}"
    DOCKER_REGISTRY_SERVER_USERNAME = local.container_registry_name
    DOCKER_REGISTRY_SERVER_PASSWORD = var.container_registry_admin_pwd

  }

  # Setup connection with azure container registry for image pulling
  site_config {
    app_command_line = ""
    linux_fx_version = "DOCKER|${local.container_registry_name}.azurecr.io/${local.acr_repo_app_name}:latest"
    always_on        = "true"
  }

  identity {
    type = "SystemAssigned"
  }

  tags = {
    environment = local.environment_tag
    application = local.application_tag
  }
}
```

### Optimize the network traffic
if you have changed the title you should here give the new term you used
As presented on the **Technical context**, on the begining of this article, we made a decision to deploy our services on the `US (United States)` zone to serve the `EU (European Union)` users. let's take a look under the hood by inspecting some network insights.

![AzureSpeedNetwork](/images/2021/11/sustainable-applications/azureLatencySpeed.png)

I'm based in Paris region, the results show a high latency when communicating with services based on the `US (United States)` zone. The insights is provided by [Azure Latency Speed Test](https://www.azurespeed.com/Azure/Latency), this tool runs latency test from my IP location to Azure datacenters around the world.


The objective of this article is to find an alternative in order to reduce the network latency and the distance of requests for static assets travelling over the network.

`Azure CDN (Content Delivery Network)` is a solution for rapidly delivering high-bandwidth content to users by caching their content at strategically placed physical nodes across the world. These edge servers are close to end-users in order to speed up the delivery of dynamic assets and minimize latency.
 
With Azure CDN `dynamic site acceleration (DSA) optimization,` the performance of web pages with dynamic content is measurably improved.

For our example, by default, the selected DSA is the `Verizon` offer corresponding to `Azure PAYG plan`, which uses some optimization technics.

Verizon network uses a combination of [Anycast DNS](https://docs.microsoft.com/en-us/windows-server/networking/dns/deploy/anycast) (`with Anycast DNS, we can enable a DNS server, or a group of servers, to respond to DNS queries based on the geographical location of a DNS client`), high-capacity support Point of Presence, and health checks, to determine the best gateways to best route data from the client to the origin.


The option to add to the resource is **optimization type** property. [Terraform possible values include](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/cdn_endpoint#optimization_type) :

- **DynamicSiteAcceleration**  
- **GeneralMediaStreaming**
- **GeneralWebDelivery**
- **LargeFileDownload**
- **VideoOnDemandMediaStreaming.**

![trafficRouting](/images/2021/11/sustainable-applications/be451c1e-0add-4ad5-8e91-ebdad2e93dc6.png)

*The capture above illustrates the communications between the Point Of Presence around the world.*

What types of optimization should this CDN Endpoint optimize for? 

Let’s focus on **DynamicSiteAcceleration** and its benefits :

* **Route optimization:**

Based on the shortest path, this algorithm chooses the most optimal path to the origin and the content is delivered fast via the most reliable route possible.
(Est-ce que cela prend en compte les contraintes de sécurité reglementaires, je m'explique si dans la route trouvée il y a des pays où il ne faut pas transiter les données? 
C'est peut-être une question bête ou peut-être tu as déjà donné la réponse mais je n'ai pas fait attention)

![vnetRouting](/images/2021/11/sustainable-applications/vnetdistance.png)

* **TCP Optimizations:** 

In a nutshell, `Transmission Control Protocol` (`TCP`) is a standard that defines how the connections should establish, work with Internet Protocol (`IP`) and the connections are established and maintained until the applications at each end have finished exchanging information.
 
`TCP slow start` is an algorithm of the TCP protocol that prevents network congestion by __limiting the amount of data sent__ over the network, as presented on the graph below. In particular, it allowed the less powerful machines to slow down a TCP connection if the machine opposite sent its information too quickly. Making an analogy with someone who talks very fast with an interlocutor having a slow understanding. It starts off with small congestion window sizes between sender and receiver until the maximum is reached, or packet loss is detected.


![routeOptimization](/images/2021/11/sustainable-applications/slow-start-congestion.png)


### Azure CDN profiles 

The Azure CDN profiles collect metrics and measure the bandwidth of connections between edge PoP (Point Of Presence) servers in order to eliminate TCP slow start.

🔴 **IMPORTANT** 

**For Azure CDN from Akamai profiles, you are allowed to change the optimization of a CDN endpoint after it has been created.**

**For Azure CDN from Verizon profiles, you cannot change the optimization of a CDN endpoint after it has been created.**


```hcl

resource "azurerm_cdn_profile" "cdnGreenApp" {
  name                = "cdn-profile-app-green-dev-001"
  location            = azurerm_resource_group.rg_green_front_fr_001.location
  resource_group_name = azurerm_resource_group.rg_green_front_fr_001.name
  sku                 = "Standard_Verizon"
}

resource "azurerm_cdn_endpoint" "cdnGreenAppEndpoint" {
  depends_on = [
    azurerm_app_service.greenAppService
  ]
  name                = "cdn-endpoint-app-green-dev-001"
  profile_name        = azurerm_cdn_profile.cdnGreenApp.name
  location            = azurerm_resource_group.rg_green_front_fr_001.location
  resource_group_name = azurerm_resource_group.rg_green_front_fr_001.name
  optimization_type   = "GeneralWebDelivery"
  origin_host_header  = "${azurerm_app_service.greenAppService.name}.azurewebsites.net"

  origin {
    http_port  = "80"
    https_port = "443"
    name       = "greenAppFrontend"
    host_name  = "${azurerm_app_service.greenAppService.name}.azurewebsites.net"
  }
}


```

### Provision an Azure CDN from the web portal

For configuring a CDN endpoint for DSA optimization by using the Azure portal:

1. From the created Web App, on the Settings section, select `Networking`

![AzureNetworkingWebApp](/images/2021/11/sustainable-applications/networking.png)


1. In `More networking features`, select `Azure CDN`
2. Register the new Endpoint and CDN profile with Standard Verizon pricing tier

![AzureCDNProfile](/images/2021/11/sustainable-applications/cdnPROFILE.png)


1. Once created, the new endpoint will appear on the Endpoints section
   
![CDNEndpoints](/images/2021/11/sustainable-applications/CDNVERIZON.png)

5. After some minutes (`2~4min`), navigate to CDN endpoint

```
cdn-endpoint-app-green-dev-001.azureedge.net
```

You see the same page that you ran earlier in an Azure web app. Azure CDN has retrieved the origin web app's assets and is serving them from the CDN endpoint.

To ensure that this page is cached in the CDN, refresh the page. Two requests for the same asset are sometimes required for the CDN to cache the requested content.
 
- For Azure CDN Standard from `Microsoft profiles`, propagation usually completes in ten minutes (add reference if any for times listed here). 
- For Azure CDN Standard from `Akamai profiles`, propagation usually completes within one minute. (same)
- For Azure CDN Standard from `Verizon and Azure CDN Premium` from Verizon profiles, propagation usually completes within 90 minutes. (same)

Furthermore, you can encounter some inconsistency, when you deploy a new version of the static content, due to the caching feature, the content will persist for some days.

Natively, the CDN will refresh its resources from the origin web app based on the `time-to-live` (`TTL`) configuration.**The default TTL is seven days**.


## Conclusion

The aim of this article was to develop applications that are carbon efficient by reducing the amount of data and distance it must travel across the network.

For the following articles, we will tackle other technics and will improve our software application by endorsing other sustainable principles.

Find the full code on my [green-software-engineering](https://github.com/kasdihacene/green-software-engineering) GitHub repository.

## Useful links

[Green software foundation](https://greensoftware.foundation/)


[Dynamic Site Acceleration on Azure CDN](https://docs.microsoft.com/fr-fr/azure/cdn/cdn-dynamic-site-acceleration#dsa-optimization-using-azure-cdn)
 
[Content Delivery Network using terrafom](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/cdn_endpoint#origin)

[Green principles](https://principles.green/)


[What is mutable vs immutable infrastructure](https://www.hashicorp.com/resources/what-is-mutable-vs-immutable-infrastructure)

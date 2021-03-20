# Auto Scaling Virtual Machine Clusters in Azure 
|  **www.binpipe.org**   [![BINPIPE](https://img.shields.io/badge/YouTube-red.svg)](https://www.youtube.com/channel/UCPTgt4Wo0MAnuzNEEZlk90A)


## What is Cloud Scaling?

In cloud computing, scaling is the process of adding or removing compute, storage, and network services to meet the demands a workload makes for resources in order to maintain availability and performance as utilization increases. Scaling generally refers to adding or reducing the number of active servers (instances) being leveraged against your workload’s resource demands. Scaling up and scaling out refer to two dimensions across which resources—and therefore, capacity—can be added.

## What Factors Impact Cloud Resource Demands?

The demands of your cloud workloads for computational resources are usually determined by:

1.  The number of incoming requests (front-end traffic)
2.  The number of jobs in the server queue (back-end, load-based)
3.  The length of time jobs have waited in the server queue (back-end, time-based)

## What is the Difference between Scaling Up & Scaling Out?

Scaling up refers to making an infrastructure component more powerful—larger or faster—so it can handle more load, while scaling out means spreading a load out by adding additional components in parallel.

### Scale Up (Vertical Scaling)

Scaling up is the process of resizing a server (or replacing it with another server) to give it supplemental or fewer CPUs, memory, or network capacity.

#### Benefits of Scaling Up

Vertical scaling minimizes operational overhead because there is only one server to manage. There is no need to distribute the workload and coordinate among multiple servers.

Vertical scaling is best used for applications that are difficult to distribute. For example, when a relational database is distributed, the system must accommodate transactions that can change data across multiple servers. Major relational databases can be configured to run on multiple servers, but it’s often easier to vertically scale.

#### Vertical Scaling Limitations

Vertical scaling poses challenges to certain workload types:

-   There are upper boundaries for the amount of memory and CPU that can allocated to a single instance, and there are connectivity ceilings for each underlying physical host
-   Even if an instance has sufficient CPU and memory, some of those resources may sit idle at times, and you will continue pay for those unused resources

### Scale Out (Horizontal Scaling)

Instead of resizing an application to a bigger server, scaling out splits the workload across multiple servers that work in parallel.

#### Benefits of Scaling Out

Applications that can sit within a single machine—like many websites—are well-suited to horizontal scaling because there is little need to coordinate tasks between servers. For example, a retail website might have peak periods, such as around the end-of-year holidays. During those times, additional servers can be easily committed to handle the additional traffic.

Many front-end applications and microservices can leverage horizontal scaling. Horizontally-scaled applications can adjust the number of servers in use according to the workload demand patterns.

#### Horizontal Scaling Limitations

The main limitation of horizontal scaling is that it often requires the application to be architected with scale out in mind in order to support the distribution of workloads across multiple servers.

## What is Cloud Autoscaling?

_Autoscaling_ (sometimes spelled auto scaling or auto-scaling) is the process of automatically increasing or decreasing the computational resources delivered to a cloud workload based on need. The primary benefit of autoscaling, when configured and managed properly, is that your workload gets exactly the cloud computational resources it requires (and no more or less) at any given time. You pay only for the server resources you need, when you need them.
```

                       AUTO SCALING OF CLOUD RESOURCES

                                         │
                                         │
                                         │
                                         │
                                         │
                                         │
LOW LOAD SCENARIO                        │            HIGH LOAD SCENARIO
                                         │
                                         │
                                         │
     ┌─────────┐   ┌────────┐            │         ┌────────┐    ┌────────┐    ┌───────┐   ┌───────┐
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     │         │   │        │            │         │        │    │        │    │       │   │       │
     └─────────┘   └────────┘            │         └────────┘    └────────┘    └───────┘   └───────┘
                                         │
                                         │         Server 1 ................................> Server N
                                         │
                                         │
                                         │
                                         │
                                         │
                                         │
                                         │

```
Distributing cloud workload via autoscaling clusters: before and after autoscaling

The major public cloud infrastructure as a service (IaaS) providers vendors all offer autoscaling capabilities:

-   In AWS, the feature is called  [Auto Scaling groups](https://aws.amazon.com/autoscaling/)
-   In Google Cloud, the equivalent feature is called  [instance groups](https://cloud.google.com/compute/docs/instance-groups/)
-   Microsoft Azure provides  [Virtual Machine Scale Sets](https://azure.microsoft.com/en-us/services/virtual-machine-scale-sets/)


**Scale Set fundamentals**

Former Microsoft architect Bill Baker is often credited with inventing the famous “cattle versus pets” metaphor of cloud scaling. If your servers are like pets, each one is lovingly raised, individually named, and carefully tended; if a pet gets sick, you nurse it back to health. If your servers are like cattle, they are all interchangeable, and you don’t treat individuals any differently by giving them cute names. If one gets sick, you shoot it and get another one. To extend this metaphor, scale sets give you a way to clone a herd of cattle, of a size you choose and with whatever breed of cow you like, on demand, as long as you’re willing to have every herd member be identical.

The first key to understanding Azure scale sets is that they are sets of identical VMs. You can customize the first cow in the herd but all the others will be exactly like the first one. The scale set itself is defined in Azure either through the portal, manually through PowerShell or the Azure command-line tools, or through an Azure Resource Manager (ARM) template. This definition tells Azure what size VM instance you want to use, what the scale set should be named, how many machines will be in it, and so on. You can customize the VM used by the scale set to include your application in three ways: by creating a completely customized VM image and supplying it to Azure, by taking a prebuilt Windows or Linux image and installing your application when the scale set is started, or by customizing the image to include container software and then loading the application container when the scale set is started. Each of these approaches has its benefits and drawbacks, but as far as the scale set is concerned they are equal. Because the scale set is an array of identical VMs, you only have to configure the instance once and then the scale set will handle creating and removing the machines. However, this also means that your application should be able to handle running with minimal on-machine configuration; if you need things like registry changes or configuration files unique to a given machine, that will be harder to automate.

When you start the scale set, Azure creates a number of objects for you: load balancers, network addresses, and so on. All of this infrastructure is contained in the Azure resource group you specify, and it’s shared between all the scale set VMs. After the required objects in the resource group have been instantiated, the scale set system will start creating VM instances up to the initial limit you set in the scale set definition. You can’t assume anything about the ordering or speed at which these VM instances will be set up—Microsoft doesn’t guarantee anything about launch time or latency.

A single scale set may contain up to 1,000 instances, but there are a number of restrictions, which are described at  [https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups](https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups). In short, you have to use multiple Azure  _placement groups_ if you want to have more than 100 VMs in a scale set, and this requires you to be mindful of the limitations on Azure load balancing (which doesn’t work with layer 4 load balancing across placement groups), storage (you must use Azure Managed Disks), and VM sources (you can’t use a customized VM that you upload if you have more than 100 instances).

Once the specified number of VM instances have been created, the scale set is considered to be running. (Actually, Azure may create some extra VMs as part of the scale set, but you’re not charged for those because they’re only used for redundancy.) What happens next depends on the way you defined the scale set. A scale set can  _autoscale_, in which case Azure will add or remove virtual machines depending on some monitored parameter. (Microsoft’s name for its autoscaling feature is “Azure Insights Autoscale,” in case you were wondering.) If you don’t enable autoscale, then you can add or remove instances either through the portal or with Azure PowerShell. Keep in mind that you’ll be charged for each instance you run for as long as you run it—the scale set itself is a convenient way to manage a fleet of VMs, but the individual VMs are billed and managed just like any other longer-lived VM you might create. Of course, one thing to remember is that running a large scale set is a good way to rack up high Azure charges—a fleet of 100 or more VMs can drive your bill up faster than you might like.

**A simple scale set application**

Let’s say you want to create a scale set that you can use for load-testing an on-premises application—that’s exactly what I wanted to use it for. In this case, I have a small simulator that I wanted to run in order to send data to a test server, but I wanted to set up several hundred of them, which would be a painfully tedious task with most other approaches. My plan was to create a scale set using a basic Azure VM image, which I would customize using the PowerShell script extension that Microsoft provides as one of the three supported customization methods. This PowerShell extension is registered on the VM (by Microsoft; you don’t have to do it, although you can register the same extension in your own individual VMs), and it runs after the VM is created and added to the scale set. There are some limitations on what this PowerShell script can do; for example, you don’t get access to the Azure Storage PowerShell extensions, so you have to plan ahead for the customizations you want the extension to apply for you. The extension I wanted to use needed to copy the application and its data files from Azure blob storage, then create some configuration data on the VM, then install a script that would run the application.

**Creating a scale set in the portal**

As I mentioned earlier, you can easily create scale sets in the portal, at least if you’re using the “new” portal (portal.azure.com). Log in to the portal, click the “+” icon, navigate to the “Compute” category, and choose “Virtual machine scale set” from the list of supported workloads. You’ll see a page with some information about placement group limitations (which I mentioned earlier). For now, you can ignore that since we’re going to create a small scale set, with just a handful of VMs. The important part of this portal blade is the section at the bottom, where the “Create” button lives. Once you click it, two new blades will appear in the portal. The Basics blade, shown on the right side of Figure 1, is where you specify the key parameters for this scale set:

-   The scale set name will be used throughout the portal. Case doesn’t matter, but you can’t include special characters.
-   OS type is self-explanatory.
-   The user name and password fields specify the credentials you want the scale set VMs to use. Remember, every VM in the scale set will have identical credentials. Azure enforces a number of password length and strength restrictions to try to keep people from choosing easily-guessed passwords: 12 to 123 characters, with at least one upper-case, one lower-case, and one numeric character.
-   The “Limit to a single placement group” control indicates whether you want this scale set to be able to grow past 100 VMs by allowing Azure to spread it across multiple placement groups. Leave it set to “True” unless you want to create a large scale set and understand the limitations of doing so.
-   The “Subscription” pulldown is used to associate your scale set with a particular Azure subscription. This is useful for billing and access control.
-   Every scale set has to live inside an Azure Resource Manager resource group. You can create a new resource group just for the scale set, or choose an existing one, with the Resource group controls.
-   The location you choose for the scale set governs the Azure billing rates used, as well as where your scale set VMs and network objects will be physically homed. While you can’t move a scale set to a new region, you can easily remove it and recreate it in another region if need be.

After you’ve filled out the controls on the Basic blade, you can click OK to go to the scale set configuration blade.

In the scale set configuration blade, you tell Azure about what you want in the scale set: how many instances, of what machine type, and running what operating system. You must also specify what the Internet-accessible FQDN for the scale set load balancer should be, whether or not you want autoscale enabled, and whether you want managed or unmanaged storage. This last choice deserves an article of its own—unmanaged disks allow you to see the individual storage objects and customize how storage is allocated to storage accounts, and managed disks don’t. Managed disks are logically and administratively simpler to work with but not as flexible. Most of these fields are self-explanatory, but the instance sizing field deserves a bit of explanation (it’s on the right-hand side of Figure 2). When you first click the “Scale set virtual machine size” field, the sizing pane appears, with a single VM instance size selected. This is the size that Microsoft recommends for your scale set deployment—in my experience, it’s usually the D1_V2 size (with 1 CPU core, 3.5GB of RAM, and 2 small data disks). Click the “View all” link and you’ll see the full catalog of VM instance sizes, so you can choose one that offers the right amount of compute power for your application. Once you’ve selected a machine size and clicked “Select,” you can click OK to dismiss the configuration blade. You’ll then see a summary blade that allows you to review your configuration choices; once you finalize the configuration, your scale set will be created and will appear in the portal. (Note that the “Virtual machine scale sets” category doesn’t appear in the default list of services; you can use the “+” icon in the left hand navigation bar to pin that category to the list for easier access.)

**Accessing your scale set**

Before you can do much of anything with your scale set, you’ll need to access it. Any time you create a VM in Azure, you have several choices to access it. For scale sets, the choices are these:

-   You can give each individual VM in the scale set its own public IP. This is more expensive, and more complex, than most applications warrant.
-   You can use a load balancer with NAT, in which case you’d probably want to set each instance of the scale set to use a different TCP port. This is the default configuration for a new scale set, starting with TCP port 50000. If you have 10 VMs in the scale set, you’ll have one public IP (for the load balancer) and connect to port 50000 for the first instance, port 50001 for the second, and so on.
-   You can set up a single machine with a public IP, then connect to it using RDP or SSH and use it as a launching point to connect to the scale set VMs on their private IP addresses.
-   For inbound traffic, you can simply create an Azure Application Gateway or load balancer instance and use it to distribute incoming traffic.

All of these configurations are outside the scope of this article, but Microsoft has documented them thoroughly so you can find what you need to set up whichever type of connection makes sense for you.

**Customizing the VM using templates, extensions, and PowerShell**

The good news: you’ve created a scale set. The bad news: it isn’t very useful, since all it is at this point is a collection of identical but unconfigured VM instances. This is where the configuration choices I mentioned earlier come into play: you can set up your application in a container and then load it into the scale set VMs, install it directly on the scale set VMs after creating the scale set, or install and configure it as part of the creation process by using a template. I prefer to use the template approach because it allows you the most flexibility. However, it is also the most time-consuming to learn and use. If you’re already comfortable with containerization technologies such as Docker, and your application is easily containerized, that might be a better way to get started quickly.

A scale set template is nothing more than a JSON file that specifies how you want the scale set configured: how many instances, what the administrator password is, and so on. In fact, if you use the GUI as described earlier in the article, the confirmation page displays a small link that lets you download a template file that can be used to implement the configuration you chose in the GUI. Using templates has several benefits, including the ability to keep your templates in a source control system such as Git. The big reason to use templates, however, is that they make it easy to do post-install customizations because you can use a template extension that runs on the VM after the VM has been instantiated. In part 2 of this series, I’ll discuss how to use templates and customization to define a scale set using PowerShell, instantiate it, customize the VM image by downloading and installing software, and start and stop the scale set when you need it to perform actual work.

**Summary**

Scalability is one of the key reasons that cloud computing has become popular, and the ability to quickly scale up a particular workload, on demand, is useful in a wide range of contexts. Microsoft has provided a simple GUI for creating scale sets in the Azure portal, so you can easily and quickly create a scale set to test applications or handle high-demand workloads.

___
:ledger: Maintainer: **[Prasanjit Singh](https://www.linkedin.com/in/prasanjit-singh)** | **www.binpipe.org**   [![BINPIPE](https://img.shields.io/badge/YouTube-red.svg)](https://www.youtube.com/channel/UCPTgt4Wo0MAnuzNEEZlk90A)
___ 

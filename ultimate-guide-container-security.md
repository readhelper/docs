# The Ultimate Guide to Container Security


**Note**: https://www.twistlock.com/2017/07/06/ultimate-guide-container-security/


Securing any type of software environment is a big task. But if you use containers, constructing and enforcing a solid security program can be especially difficult.

That’s because a containerized environment involves so many different layers as compared to other types of infrastructure. With virtual machines, you have only a host operating system (OS), a guest OS, and a guest application environment to secure. On bare metal, and in most types of cloud-based environments, the security situation is even simpler because there are fewer layers of software.

In contrast, in a production container environment, you have a number of different layers and tools to secure. In addition to the host OS and the container runtime, you have an orchestrator, a container registry, images, and probably several different microservices within your application.

How do you keep each of those pieces secure and hardened against attacks? The following guide explains how to secure all of the layers in your container stack, from top to bottom.

## Host Operating System

The OS that hosts your container environment is perhaps the most important layer of the stack to secure, because an attack that compromises the host environment could provide intruders with access to everything else in your stack.

Fortunately, the host OS is also probably the easiest part of the stack to secure. The types of operating systems that are used to host containers are not fundamentally different from those that admins have been using for years to host other types of workloads. In most cases, your host OS is a Linux distribution, and the principles you use to [harden any type of Linux environment](https://www.twistlock.com/resources/securely-configure-linux-host-run-containers/) apply when you’re dealing with containers.

The most important considerations to keep in mind for hardening the host OS include:

*   Minimize attack vectors_. A Linux-based OS whose sole job is to host a Docker environment doesn’t need to have much running. Minimize opportunities for attack by eliminating all but the essentials from your host environment. You can do this manually by taking a Linux distribution of your choice and stripping it down, or you could use a minimalist distribution [like Alpine](https://alpinelinux.org/).
*   Enforce strict access control_. If your host OS’s only role is hosting a container environment, you don’t need many accounts. In fact, in most cases, all you need is a root user (whose access can be locked down by allowing logins only from specific remote hosts) and dummy user accounts (which should also be locked down so that they can access only the specific services or resources that they require to do their jobs). Tools like SELinux and AppArmor can help to create and enforce rigid access control policies.

## Container Runtime

The container runtime is one of the most difficult parts of a container stack to secure because traditional security tools were not designed to [monitor running containers](https://www.twistlock.com/use-cases/container-runtime-defense/). They can’t peer inside containers or establish good baselines for what a secure container environment looks like.

That’s why you should be sure to implement the following in order to keep your runtime secure:

*   Establish a baseline for your container environment in a normal, secure state. Perform real-time scans of running containers and compare the results to the baseline in order to detect anomalies that could signal an attack.
*   Focus on securing your application, rather than relying on network-level security tools to keep you safe. Firewalls and other types of perimeter defenses don’t work well in a containerized environment. (That said, you should certainly take basic precautions to secure your networks—For example, be sure that encryption is configured for overlay networks.)
*   Ensure that running containers are stopped and replaced with new containers whenever you make an update to your applications or services. In other words, make sure to keep your containerized infrastructure immutable. This is safer than attempting to perform live updates on running containers, which leads to configuration drift and poor enforcement of security policies.
*   Remember that your running containers are only as secure as the application code that powers them. All of the traditional rules for writing secure code and vetting it for security after it is written apply. In this regard, the [shift-left security concept](https://www.twistlock.com/2017/05/31/shift-left-security/) can come in handy.

## Registry

One of the features that sets containerized environments apart from most traditional environments is registries. Registries provide a convenient, centralized source for storing and downloading application images. (This is not a totally new idea, of course; most Linux distributions have used software repositories in a similar way for a long time, and “app stores” function in a similar way as well. But container registries are different in that they are essentially the only way to obtain and run a containerized application, whereas in traditional environments, it’s possible to install applications without using a repository or app store.)

Because the registry is central to the way your containerized environment operates, it’s essential to secure it. Intrusions or vulnerabilities within the registry provide an easy opening for compromising your running application.

Securing the registry involves several considerations:

*   Locking down the server that hosts the registry in order to mitigate the risk of attack there.
*   Using secure access policies.
*   Running an image scanner to help [detect vulnerabilities within container images](https://www.twistlock.com/2017/02/21/integral-container-security/).

## Container Images

The task of securing container images overlaps to a large extent with securing registries, since registries are where your images live. But because image security involves some additional considerations, it deserves a section of its own.

To secure images, keep the following pointers in mind:

*   Images should contain the bare minimum amount of code necessary to run whichever service or application you are creating the image for. Exclude any non-essential services from the image. For example, in most cases there is no reason to include an SSH server inside a container image because you can log into the container in other, more secure ways. So don’t include SSH.
*   The purpose of images is to provide a blueprint for creating an application or service container. Don’t use images for other tasks, like hosting source code. Indeed, this may be convenient, but there are better, safer ways to host source code, such as using a code repository. This is a mistake that Vine famously made last year, when it placed source code inside container images that [turned out to be publicly available](https://avicoder.me/2016/07/22/Twitter-Vine-Source-code-dump/).

## Orchestrator

The orchestrator is the brain that keeps all of your containers running smoothly. It’s another essential component of a container stack.

One important thing to bear in mind about orchestrators is that they are not security tools. They provide provisioning, but they are not designed to detect intrusions or vulnerabilities. In other words, don’t make the mistake of assuming that Kubernetes, Swarm or whichever orchestrator you use will do much on its own to keep your environment secure.

On the contrary, you need to take precautions to ensure that the orchestrator itself remains secure and does not become a liability. You can do so using these principles:

*   Install the orchestrator from an official, trusted, up-to-date source (just as you should do for any type of mission-critical application).
*   Configure the orchestrator to provide high availability and automatic failover to the extent possible. This will help to mitigate the impact of DDoS attacks.

There are other steps you can take to secure your orchestrator. They vary depending on exactly which orchestrator you use. The Kubernetes folks offer some useful tips [here](http://blog.kubernetes.io/2016/08/security-best-practices-kubernetes-deployment.html). For Swarm, the information in this [Docker reference guide](https://success.docker.com/Architecture/Docker_Reference_Architecture%3A_Securing_Docker_EE_and_Security_Best_Practices) can be helpful (although the guide is not written solely with Swarm security configuration in mind). Additionally, Twistlock just recently ran an [Ultimate Guide to Container Orchestrators](https://www.twistlock.com/2017/05/22/container-orchestrators/) post, so if you still want more details, check it out.

## Persistent Storage

The persistent storage layer of your containerized environment can take different forms, depending on which type of solution you use. [Docker Data Volumes](https://docs.docker.com/engine/tutorials/dockervolumes/) is one approach. [Flocker](https://github.com/ClusterHQ/flocker) (which lives on as an open source project even though the company behind it, ClusterHQ, shut down in late 2016) is another. So is [Portworx](https://portworx.com/), which provides a commercial container storage solution.

The details of securing your container storage will vary depending on exactly which type of storage architecture you use. But in general, some basic security principles apply:

*   If a container does not need write access to a storage directory, limit it to read-only access.
*   Use a file system that enables roll-back so that you can undo any changes to your data if necessary.
*   As with several other parts of the container stack, access control is key. Make sure that only containers that need access to a shared storage directory have access. In addition, make sure that no users or applications on the storage server have access to the storage system unless they require it. Last but not least, remember that chmod is your friend. Use it to provide fine-grained access control for specific files or directories where appropriate.

_**[Contact us for an evaluation](https://www.twistlock.com/get-twistlock/) to learn how Twistlock can help your organization secure your container environment, or subscribe to our blog for more regular updates on container security news and tips.**_

*   Categories:
*   [Application Security](https://www.twistlock.com/category/application-security/)
*   [Container Platform](https://www.twistlock.com/category/container-platform/)
*   [Container Security](https://www.twistlock.com/category/container-security/)
*   [Containers](https://www.twistlock.com/category/containers/)
*   [DevOps](https://www.twistlock.com/category/devops/)
*   [Docker Security](https://www.twistlock.com/category/docker-security/)
*   [Enterprise Security](https://www.twistlock.com/category/enterprise-security/)
*   [Linux Security](https://www.twistlock.com/category/linux-security/)

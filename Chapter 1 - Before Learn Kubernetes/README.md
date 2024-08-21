## Chapter 1 : Before Learn Kubernetes

### 1.1 Starting from Monolith and Microservices

**Monolith** 

Monilithic model is application models which is all application menu, featurem anything in one application model. This model is tradional and having some troubl, example what if happening loud traffic network it will make more slow in service because it only 1 handled application.

**Microservices**

Microservices are an application model where applications are broken down into pieces, so they can handle the tasks in a better way, and they can communicate with each other.

![Hos (1)](https://hackmd.io/_uploads/BJJN1XmsA.png)

### 1.2 Problem of Microservices

Using microservices has its own homework in development, because every development section wants to deploy an application, the applications deployed are many, unlike in Monolith where only one application is deployed and it's done right away.

Also, in the case of wanting to scale the application for some reason, the development section will have difficulty if they scale manually, there is no connection between applications. Plus, other applications that have not been scaled. This will be a disaster especially for the Development or DevOps team.

### 1.3 Actually, there is a Virtual Machine but...

Yes, according to the title, there is actually a Virtual Machine where the architectural model is similar to Microservice, you can see in the picture :

![Hos](https://hackmd.io/_uploads/BJm-yXQoC.png)

They are similar actually, but the case in Virtual Machine is also the same where scaling the application also takes a long time and is difficult, also to run Virtual Machine needs to run Each OS that is running, so when run Host OS / main OS will be heavy resources.

But different from containers / microservices, each application that runs will be managed by the same Container Manager and uses only one OS.

Try to see the picture :

![Hos (2)](https://hackmd.io/_uploads/B1rJ-77sA.png)

If you want to run an application, the system will immediately do the appropriate work without having to think about where the applications are stored.

### 1.4 So, What Does This Have to Do with Kubernetes?

So, Kubernetes is an orchestration tool for Machine Containers, how do you understand? Like this... like an orchestra in a Music Stage, which needs a conductor to lead the notes and tones. Kubernetes is the conductor, and the Music Players are the Application Containers.

So that's it, now it's time to learn Kubernetes. You will explore Kubernetes in the next chapter.

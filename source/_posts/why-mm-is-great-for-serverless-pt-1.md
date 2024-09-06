---
title: >-
  Why Modular Monolith is a great starting architecture for a serverless application? (Part 1)
date: 2024-09-06 13:53:34
tags:
---

# Embracing the Serverless Modular Monolith: A Path to Scalable and Efficient Architecture

At PostNL, our ambition to become the leading postal tech company continues at full speed. As we work towards being the favorite deliverer in the Benelux area, we must ensure our technology infrastructure is robust, scalable, and adaptable. In this blog post, I would like to give an overview of a system architecture style called "Serverless Module Monolith". We adopted this architecture style for multiple benefits and it seems to be working well for various types and scales of platforms for us. It also allows us to scale up even more when needed, for example when we would like to divide a bigger platform to smaller parts and assign each part to new teams.

This post will be a two part series:

- Part 1: We will explore the various platforms at PostNL, including planning, operation execution monitoring, and enabler platforms, each with unique requirements. We will discuss different architectural styles: monolithic, fully serverless, and serverless microservices, highlighting their pros and cons. Finally, we introduced the concept of Serverless Modular Monoliths.
- Part 2: We will discuss how we are building Serverless Modular Monoliths and the practical benefits they offer. Finally, we will conclude the series with a recap.

## The Challenge of Delivering at Scale

To meet the ever-growing demands of parcel delivery, we need a sophisticated technology stack that can handle everything from planning to real-time operations. Our engineering teams work hand in hand with our colleagues in the field, ensuring that every parcel reaches its destination as efficiently as possible. But this level of efficiency doesn't happen by accident, it requires a well-planned and executed system architecture.

### Different Platforms, Different Needs

To deliver your parcels as quickly and as efficiently as possible, a lot of preparation, planning, and operational execution monitoring is necessary. As a Principal Software Engineer, I work closely with one of these departments, where we have different types of platforms. We can group them into three high-level categories. Let me give you an overview of these platforms from a high level.

1. **Planning Platform:** One of these platforms focus on the planning aspect of our logistics. PostNL has many sorting centers, each with different parcel sorting, holding, and processing capacities from the parcels’ perspective. From the staff’s perspective, the sorting centers have different shifts, working hours, and the capacity of colleagues who work there. To utilize all our resources efficiently, we use machine learning algorithms that are designed and industrialized to answer specific questions during the planning phase. The planning platform has low scaling requirements, as it does not have many concurrent users executing simulations at the same time; however, it requires significant compute power because it needs to collect and process an enormous amount of data to feed and run our industrialized algorithms that power our simulations.

2. **Operation Execution Monitoring Platforms:** These platforms handle real-time data, ensuring that daily operations run smoothly. They are designed to be responsive to the constant flow of information coming in from the field. This includes tracking the number of parcels arriving at sorting centers and monitoring for any operational anomalies. As these platforms must process large amounts of data in near real-time, they have medium compute and scaling needs, balancing the demands of immediate responsiveness with the ability to scale up as necessary.

3. **Enabler and Integration Platforms:** Platforms like our IoT-Platform fall into this category. They process massive amounts of data, with scaling needs that are through the roof. For instance, our IoT-Platform processes over 2 billion events daily, tracking assets across the Benelux area. These platforms either generate raw data or enhance and facilitate its flow, ensuring that consumer platforms can effectively process the information.

Among these platforms, some are using a specific type of systems architecture that exploits the scalability and simplicity of serverless together with a modular monolith approach, which makes deployment and maintenance easier. However, after the first deployment, the platform’s behavior is similar to a microservices architecture; scalable by default, isolated with the integration capabilities of AWS, and with additional benefits that I will dive into in detail in the next sections.

## The Evolution of System Architecture

When designing a system architecture, the choices we make have far-reaching implications. Traditional monolithic architectures offer simplicity but struggle with scalability and reliability. On the other hand, microservices provide flexibility and scalability but at the cost of increased complexity. So, where does the Serverless Modular Monolith fit in?

As PostNL, we prefer to build AWS Serverless applications. However, we still need to choose an architectural style that will allow us to build and evolve our system with the needs of the business.

Since we are working with Serverless, I prepared examples in this environment. Let's have a look at our options.

### Monolithic Stack

This one is a bit extreme, however if you would like to operate a simple systems architecture, you can achieve it with this approach by adding all your functionality in an ECS Fargate container hence making a monolithic software architecture where all necessary functionality in one container.
By making the right choices with this systems architecture, we can actually keep it running for a long period of development.

![monolithic-stack](/images/posts/2024/monolithic-stack.png)

With a monolithic system architecture, the success of the project heavily depends on your software architecture. This is mainly because your system architecture is a single unit. There is a significant risk that if you do no not strictly follow development principles and best practices, your software architecture could become a [big ball of mud](https://en.wikipedia.org/wiki/Anti-pattern).

![big-ball-of-mud](/images/posts/2024/bbm.png)

### Fully Serverless Stack

You can also go full serverless without following any architectural style, adding components as new features come along.

TODO: Fix here
![GIF - Serverless stack evolving]

But consider this for a moment. If you keep blindly adding more and more serverless components, how are these any different from each other?

![serverless-vs-bbm](/images/posts/2024/merged-bbm-serverless.png)

### Serverless Microservices

![serverless-microservices](/images/posts/2024/serverless-microservices.png)

This is probably where you should end up if your system keeps evolving.
However, if you start with this implementation immediately, you might be skipping a step in the evolution of your system.

Why?

First of all, getting the bounded contexts of such microservices right is tough. Getting them right on the first try is close to impossible unless you have done it a few times before.

There is also another aspect.

You are adding significant work for dependency management right at the beginning.
Now you have to start thinking about deployment strategies right from the start.

Isn't there a better way to have all stacks available in your AWS CDK App? That would make deployment orchestration so much easier.
And we know from experience that AWS CDK can handle extremely complex deployments.

Yes, there is a way to achieve that, with Serverless Modular Monoliths. In Part 2 of this series, we’ll dive deeper into the specifics of how we build Serverless Modular Monoliths in PostNL. Stay tuned for a closer look!

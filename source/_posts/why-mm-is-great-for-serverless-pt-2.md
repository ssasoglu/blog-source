---
title: >-
  Why Modular Monolith is a great starting architecture for a serverless application? (Part 2)
date: 2024-09-06 13:55:48
tags:
---

In Part 1, we explored the various platforms at PostNL, including planning, operation execution monitoring, and enabler platforms, each with unique requirements. We discussed different architectural styles—monolithic, fully serverless, and serverless microservices—highlighting their pros and cons. Finally, we introduced the concept of Serverless Modular Monoliths, setting the stage for a deeper dive in Part 2. In this part, we will discuss how we are building such systems and the practical benefits they offer. Finally, we will conclude the series with a recap. Without further ado, let's start!

# Serverless Modular Monoliths

![smm](/images/posts/2024/smm.png)

The first question is "How do we build our Serverless Modular Monolith systems?"

"Serverless Modular Monolith" name might not make any sense because how can a system be serverless and a monolith at the same time? It requires some additional context. Please let me explain.

It is easier to explain the concept through an example, so here is an example system:

![serverless-vs-bbm](/images/posts/2024/example-system.png)

In this imaginary platform, we deploy a single AWS CDK Application which resides in a separate GitHub repository.

![platform-repo](/images/posts/2024/platform-repo.png)

There is no business logic code here. However, the orchestration of the modules that compose the overall platform is stored here with versioning of the underlying modules. So, in essence, the overall system is a single CDK application hence the monolith. If you look at it from this perspective, the entire platform deployment happens at the same time on the first deployment and it happens as a single unit. It will succeed or fail altogether.
 
![orchestration](/images/posts/2024/orchestration.png)

## Serverless Modules

The serverless modules are stored in their respective Git repositories, where the final artifact is an AWS CDK Stack library that is versioned and stored in our package manager.

![module](/images/posts/2024/module.png)

The platform repository has dependencies on all the modules with versioning, manages the connections between these modules, and determines the data flow. AWS CDK manages these dependencies for us during the deployments. Since we are in a serverless environment, once the CDK app is successfully deployed, each module can work in isolation, assuming they are designed correctly, and they will scale up and down automatically, behaving similarly to microservices.

While building these so-called “Modules,” we follow some best practices:

- **Single Responsibility:** Each module has a single responsibility and is independent of any other module.
- **Isolation and Testing:** Modules can be deployed individually for testing purposes, allowing extensive integration tests to validate behavior before they are plugged into the platform.
- **Versioning:** Each module is versioned so it can progress independently of other modules.
- **Scalability:** Modules scale up and down depending on the hotspots within the platform, thanks to their independence and connection through integration services.
- **Cost Efficiency:** We can tag each module to track individual costs and improve cost efficiency if necessary.
- **Logical Boundaries:** Modules have logical boundaries, which, combined with their single responsibility, make the overall system easy to reason with. This approach ensures high developer experience and low cognitive load during development.

## Practical Benefits in Action

Creating modules for low cognitive load and ease of deployment when combined with the serverless environment, opens up a number of possibilities such as:

- Smooth version migrations with backward compatibility
- Side-cars,
- A-B Testing.

### Version migrations

In the first scenario, we have been running the Rollcage-Processing-Module in v1 for some time and we are now ready to deploy v2.
With the current approach, we have the possibility to deploy two versions of the same module and there are multiple ways to achieve that.

- One could be a name change in the library and deploy it like a new module.
- Another option could be hiding the new version behind a feature flag and deploy the same module with feature flag enabled (using StackProps)

![versioning-1](/images/posts/2024/versioning-1.png)

When we do that, we can run the two versions together and we can run them together until we are ready to phase out v1.

- For example, until the caches catch up with each other
- If this was an outlet module where other teams were dependent on your system, we can wait until all teams migrate to v2.

![versioning-2](/images/posts/2024/versioning-2.png)

Once the migration requirements are completed, we can remove v1 and continue our development.

This approach will ensure backward compatibility during the entire process and allows stakeholders to catch up at their own pace using communicated sunset dates for older versions.

### Side-cars

Another possibility is to add a sidecar module. This pattern is named “Sidecar” because it resembles a sidecar attached to a motorcycle. In this pattern, the sidecar is attached to a parent application and provides supporting features for the application. This allows us to decompose some features into a separate module.

![sidecar](/images/posts/2024/sidecar.png)

### A-B Testing

Also, it is easy to do A-B Testing with this approach. Let’s say that you want to add analytics and see two possible ways to extract data from the system, but you do not know which one would work better. This is an exploration case where we don’t know the outcome of the research yet, and the approach includes a “Sacrificial Architecture.” With our approach, it is easy to connect both and see which one performs better. Simply sacrifice the other once we are happy with the results.

![a-b-testing](/images/posts/2024/a-b-testing.png)

## Evolving Towards Microservices

As our platforms grow, there's always the potential to evolve them into fully distributed microservices. By carefully managing the bounded contexts within our platforms, we can split a modular monolithic platform into multiple microservices, each with its own AWS account. Architecture remains flexible and scalable as we continue to grow.

The ability to evolve from a modular monolith to microservices without a complete architectural overhaul is one of the key benefits of the Serverless Modular Monolith. This approach allows us to balance the need for stability with the flexibility to adapt and grow.

## Recap

Here is a recap of what we have covered so far in Part 1 and Part 2 of this series.
We chose Serverless Modular Monoliths because of the following (Key) benefits:

Key benefits:

- Small modular design mitigates a lot of problems and opens possibility to evolve towards microservices
- Deployment becomes a single command `cdk deploy`
- Offloads complex dependency management to AWS CDK especially in the beginning phase of a project
  - We do not need to think about deployment strategies right from the start

Other benefits:

- Less cognitive load for developers and easy to reason with the overall system.
- Loose coupling, high functional cohesion
- Enables cost tracking and management
- Balanced exploration and exploitation
  - If we need something fast, we can reuse what we have built so far.
  - If we need to experiment something, it is easy to plug it in the platform and see how it behaves.
- Only the first deployment is a monolith, after that in each deployment AWS CDK is smart enough to only reflect the changes or add the new components.

## Conclusions

I hope you enjoyed this series and thank you for reading so far. To conclude our journey, let me write a few more sentences to consider.

If you don't have a plan for how your system will evolve, it won't survive the test of time. By starting with a serverless modular monolithic architecture, we can build a solid foundation that supports growth and evolution for our systems.

This blueprint has helped us at PostNL, and I hope it can serve as inspiration for you as well.

If these architectural evolution is interesting for you, definitely have a look at the concept "Evolutionary Architectures".

Happy coding and see you next time!

## References

- [Anti-pattern: Big Ball of Mud](https://en.wikipedia.org/wiki/Anti-pattern)
- https://pretius.com/blog/modular-software-architecture/
- https://www.thoughtworks.com/en-us/insights/blog/microservices/modular-monolith-better-way-build-software
- https://medium.com/design-microservices-architecture-with-patterns/microservices-killer-modular-monolithic-architecture-ac83814f6862
- https://serverlessfirst.com/emails/why-monolithic-deployments-make-sense-for-small-serverless-teams/
- PostNL facts and some numbers about parcel delivery: https://www.postnl.nl/en/about-postnl/about-us/parcels/

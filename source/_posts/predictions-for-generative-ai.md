---
title: "Predictions for Generative AI"
date: 2025-03-14 15:15:00
tags: 
- ai
- generative-ai
- llm
- cursor
---

Like any other tech company, we are also discussing how we can utilize Generative-AI to its full extent in my current company as well. And we should! Because AI is changing the world in the ways that we wouldn't be able to dream of at all. A lot of jobs are becoming obsolete and many new professions are emerging. This is not news to me, it is the cycle of innovation.

When the [industrial age](https://en.wikipedia.org/wiki/Industrial_Age) first came, a lot of relevant jobs of that time became obsolete. At the same time, a number of new professions emerged. We started to manufacture faster and more efficiently. The current ~hype of~ Generative-AI in the [information age](https://en.wikipedia.org/wiki/Information_Age) resembles a similar situation to me, at least for now.

If we look at the [hype cycle](https://en.wikipedia.org/wiki/Gartner_hype_cycle), we are still in the "Peak of Inflated Expectations" phase, but things are slowly settling in. I have been using Generative-AI since the first generation products. Not consistently, but I have looked in, tried some stuff and stepped out. After some time, I came back again, checked the current status quo, and repeated that within cycles in the last two years. Looking back, I am amazed at how much we have progressed, if I think that I started my experimentation with [ChatGPT](https://chatgpt.com/auth/login) and [Midjourney](https://www.midjourney.com/home) when they were initially released.

![hype-cycle](../images/posts/2025/hype-cycle-general.png)

Today, I am using Generative-AI solutions on a daily basis. They have evolved to be essential tools in my way of working. At the time of writing, I am using Cursor AI, ChatGPT, and Claude every day. Negotiating and expanding my ideas with their help. Cursor AI is suggesting what to write next even in this document, depending on the context of what I have written so far and the other files in this blog post repository. The integrated IDE allows the AI to have more context than it had a few years back.

And this brings me to the topic of **context** while using AI.

For AI to provide truly effective solutions, it must grasp the context it is in. This is exactly what Cursor AI is doing for software development. Being integrated with IDE gives it a superpower, I find the suggestions coming from Cursor AI to be a lot more relevant than its competitors.

## What AI still cannot do?

I still need to relay a lot of context in my individual prompts or adjust the system prompt to give an overall direction to get relevant results. What I believe is that AI still needs to be able to "zoom in" on the specifics of a task and "zoom out" to understand the broader context of its operating world without me spoon-feeding every bit of information to get relevant results. We are currently lacking that ability.

What matters for humans is that even if we use an AI, the AI should understand the context that we are in. What kind of person I am, what kind of choices do I make normally? Not just during a coding session, also my lunch preferences. I would like to be able to ask AWS Serverless questions until lunch time. To be able to help me with that, the AI requires to zoom in to the AWS Serverless aspect of my life, but then later on for example, I should be able to say: "I'm finished with this coding session and I'm going for lunch. What do I eat today?". It needs to zoom out again and know my general preferences in choosing lunch.

Of course, this means that the AI will analyze me in detail as a person, probably know more about me than I do about myself with my biases in thinking, my prejudices while approaching a topic. Privacy and security will become a top concern.

## Predictions

### Privacy and security

As the previous section suggests, privacy and security of the information that AI holds for me will become a top concern. Maybe we need a [YubiKey](https://en.wikipedia.org/wiki/YubiKey) for it. The information should be secure and with me all the time, not available online or on the cloud. When I plug the hardware on my computer, I should be able to give AI the access to it. This information should alter the system prompt automatically with **_my context_**. This would require a special protocol that would be understandable by all LLMs.

### Code that only AI can understand

There is not a sorting algorithm that is invented by an LLM yet, but maybe in the next generation there will be. And humans probably won't be able to understand how it works. AI can already dream [new proteins](https://cen.acs.org/physical-chemistry/protein-folding/Generative-AI-dreaming-new-proteins/101/i12). I see no reason that it can't dream a new sorting algorithm as well. We are just not there yet.

We are discussing this topic a lot with my colleague [Luc van Donkersgoed](https://lucvandonkersgoed.com). You should read what he has to say in his blog post: [Are humans the limiting factor in AI-assisted software development?](https://lucvandonkersgoed.com/2025/02/09/are-humans-the-limiting-factor-in-ai-assisted-software-development/)

In my opinion, the way forward will be in a hybrid solution.

#### AI-augmented application development

We will have custom applications where AI-augmented software engineers will still design, implement, and maintain critical applications, especially the ones that matter for any form of life on our planet and survival. Creating next-generation AIs should also fall into this category. These engineers will still need to know a lot about computer science and be in complete control of how these applications are operating. Decision making will be in control of us, humans.

These applications will still look like the traditional software development that we have today, however empowered with AI.

#### AI-powered application development

We will also have other applications where AI will probably do everything where we (software engineers) will only help design the application and shape the functionality instead. These will be applications for mundane tasks or will be categorized as non-critical. I see that these applications will have two stages:

* Software engineers will work with an AI and create the functionality in high overview. They will together construct the **_system prompt_** of the application.
* This high overview design will be processed in multiple iterations in such a way that everything that is required to implement and run it is dissected into such small tasks that the code behind them will not matter and humans won't need to understand the code behind that functionality. AI will manage it for us, literally creating the **_single shot prompts_** to generate the same output.

We will commit and version the above text in a prompt library, together with the LLM configuration instead of a code repository.

### Digitalization will be of utmost importance

Digitalization will be the foundation for AI effectiveness. Moving away from gut feeling decisions towards a data-driven approach will help pinpoint anomalies, understand root causes, and facilitate proactive decision-making. Not just that, but also it will help the AI to build your **_company's context_**.

Companies who favor organic communication without written documentation will degrade in efficiency while their competitors will be feeding and nurturing the **_company's context_** with process documentation, direct AI access to data, metrics, and benchmarks of previous years. This will help them optimize existing processes, forecast the future with the data from previous years, and help the business make the right investments by analyzing the **_company's context_** and the market.

## Final words

Here you have it. My experience with Generative-AI so far and my predictions for the near future. And I should say, I am excited for the future of software development. It is for sure that we (software developers) will need to change and adapt. The role itself will evolve and become augmented with AI, but hey, that's what we do every day right? We are at the heart of new technology.

Let me know what you think in the comments below.

Happy coding!

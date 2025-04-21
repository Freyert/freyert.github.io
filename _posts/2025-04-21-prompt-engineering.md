---
layout: default
author: Fulton Byrne
title: "Prompt Engineering Basics"
---
Prompt Engineering rests at the heart of Generative AI. Today, many techniques exist to tune prompts to reach a desired output. [DAIR.AI](http://DAIR.AI) compiled a [useful list of prompt techniques](https://www.promptingguide.ai/). I want to practice these different prompting techniques from simplest to complex. I want to apply these techniques to the open-ended task of generating user stories for a fictional product team at Big Dog, the world’s top canine accessory company, developing new toys for dogs.

# Zero-shot Prompting

I think of this layer of prompting as the instruction. Today, most people use Gen AI like a search engine. You prompt with a question and receive an answer.

```  
USER: What are dogs?  
SYSTEM: Dogs are domesticated mammals, scientifically known as *Canis lupus familiaris*. They descended from wolves and have been living alongside humans for thousands of years—maybe as far back as 15,000 years or more…   
```

While it may feel like a conversation, it is good to remind ourselves of the underlying mechanism of a generative AI system. My input is the beginning of a document and the AI system is predicting what the rest of the document *could* look like.

```  
USER: generate a user story  
SYSTEM:

Sure\! Here’s a general example of a user story, written in the classic format used in agile development:

---

**User Story:**  
 As a **frequent online shopper**, I want to **save multiple delivery addresses to my profile**, so that I can **quickly select one during checkout without re-entering the details each time**.

---

Let me know the context you're working with (like a specific product, feature, or audience), and I can tailor it to your needs\!

```

# Multi-shot Prompting

It is sheer luck that the AI generated an e-commerce relevant user story. We want to make sure the user stories directly relate to our business, experience, and also follow a consistent format. In the long run we also want to reduce the amount of “conversation” the AI outputs because tokens cost. Let’s look quickly at the costs for [OpenAI’s featured models](https://platform.openai.com/docs/models) as of April 21, 2025:

| Model | $/1M tokens ingested | $/1M tokens output |
| :---- | :---- | :---- |
| GPT-4.1 | $2.00 | $8.00 |
| o4-mini | $1.10 | $4.40 |
| o3 | $10.00 | $40.00 |

I like to look at physical constraints such as cost because they really help guide what matters as we build our solution. So we will optimize on terse output through verbose instructions in the input.

Multi-shot is exactly the tool for this optimization. We provide input/output examples in the format we expect. So now we make a list of inputs and outputs to get the model to tune to our format.

```  
USER:  
Generate a user story for a dog with tons of energy: As a dog with tons of energy, I want a treadmill so I can run off all my energy, improve my fitness, and mood.  
—  
Generate a user story for a small dog: As a small dog, I want a comfortable purse to be carried in, so that I can spend more time with my human.  
—  
Generate a user story for a dog with sensitive skin: As a dog with sensitive skin, I want a gentle shampoo and moisturizer to keep my fur and skin clean and moisturized.  
—  
Generate a user story for a dog with matted fur:

SYSTEM:  
Sure\! Here's the user story for a dog with matted fur:

**As a dog with matted fur, I want a detangling brush and a grooming routine, so that my coat stays smooth, clean, and comfortable without painful knots.**

Want me to keep going with other quirky or specific dog user stories?
```
 
The response follows our format without some of the flourishes from before. More importantly, the response pertains to Big Dog’s market: dogs. I can even drop specifying the user from my next prompts and still receive relevant answers:

```  
USER: Generate a user story  
SYSTEM:

Absolutely\! Here's a fresh one:

**As a curious puppy, I want a variety of chew toys, so that I can explore textures, stay entertained, and avoid chewing on furniture.**

Let me know if you'd like a story for a specific type of dog or behavior\!
```

What are the benefits here:

1. Less input tokens.  
2. More creative output, while remaining relevant.  
3. Output remains format constrained.

# Chain-of-Thought Prompting

Zero-shot establishes the task, Multi-shot provides structure to the task output, Chain-of-Thought introduces *reasoning*. The key to remember is that it’s more of *pseudo-reasoning*. Based on previous examples and explanations it will predict what a new example and explanation would look like.

Taking the input user stories from before, I will extend them to include reasoning as to why we decided to implement the features and the impact:

```  
USER:  
Generate a user story for a dog with tons of energy: As a dog with tons of energy, I want a treadmill so I can run off all my energy, improve my fitness, and mood.  
Why should we implement this story:  
\* Fitness conscious dogs represent 90% of our total addressable market.  
\* Pent up energy accounts for $2000 in furniture damage per capita.  
\* Given our relationship with manufacturers we are able to deliver dog treadmills for $200 total cost.  
—  
Generate a user story for a small dog: As a small dog, I want a comfortable purse to be carried in, so that I can spend more time with my human.  
Why should we implement this story:  
\* The small dog market is a high growth market.  
\* Luxury handbags generally command high markups.  
\* We were able to develop a relationship with a luxury clothing brand that helped grow the Big Dog brand.  
\* The luxury partnership directly accounted for 100,000 new customers in the first year.  
—  
Generate a user story for a dog with sensitive skin: As a dog with sensitive skin, I want a gentle shampoo and moisturizer to keep my fur and skin clean and moisturized.  
Why should we implement this story:  
\* Beauty products command high markups.  
\* Rising global temperatures have extended allergy seasons which generally exacerbate sensitive skin issues.  
\* We surveyed 100,000 customers and 60% indicated a need for gentler dog skin/fur care products.  
—  
Generate a user story for  
—  
SYSTEM:  
a senior dog:  
**User Story:**  
 *As a senior dog, I want a supportive orthopedic bed so I can rest comfortably and reduce joint pain.*

**Why should we implement this story:**

* The aging pet population is growing rapidly, with senior dogs now making up 40% of households with dogs.

* Orthopedic pet products typically command higher margins and generate strong customer loyalty.

* Comfortable senior dog solutions lead to increased satisfaction and retention among long-time customers.

* Offering premium comfort products enhances our brand’s perception as a lifelong pet care provider.
```
  
Now we get user stories with what appears to be reasoning as to why we should implement them. We all would do well to remember that the AI is attempting to complete a document. The AI is not reasoning, but *is* incorporating data that it has seen before to create a document.

Put another way: the AI is essentially “brain storming” with us. We need to validate the information before we decide if we should implement the user story.

# Conclusion

I consider these three prompting techniques the atoms of prompting. We populate the model’s context with examples that help it to complete our request. More advanced prompting techniques generally apply these base techniques over multiple steps. For example, summarizing all of Herman Mellville’s *Moby-Dick* requires us to first summarize each chapter with Zero-shot prompts because the entire book will not fit in the context window at once.

What next steps would further improve this user story generating model? The brain storming presents an extremely helpful tool, except we need to validate the voracity of the output. Potentially, leveraging techniques that encourage reasoning and tool use such as [Automatic Reasoning and Tool-use](https://www.promptingguide.ai/techniques/art) or [ReAct](https://www.promptingguide.ai/techniques/react) to encourage the model to incorporate high quality, external sources into the evidence.


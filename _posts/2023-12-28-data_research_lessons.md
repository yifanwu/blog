---
layout: post
title:  "Lessons learned for running data experiments"
date:   2023-12-28 14:02:49 -0800
categories: llm learning
---

I spent six months working on the data side of pretraining at a rocket ship LLM startup. More specifically, I collected and processed large volumes of training data. I had no prior LLM/deep learning experience---you can read about what topics of data research is in [my other post]({% post_url 2023-12-28-data_work_for_training_llms %}).

For context, I have a graduate degree in CS and previously founded a tooling startup.  I hope to articulate what I’ve learned in the past six months in hopes of not repeating the costly mistakes again, and to share with others. 

Data work is usually very nuanced and creates a lot of complications.  I found myself losing track of details and making mistakes. Mistakes are usually easy to correct and cope with if your development loop is fast. But data pipelines take time to execute and model experiments take a while to train and inference, so mistakes are more costly. And this lead to general low velocity.

I learned a few important lessons: derisking, clear hypothesis testing, and don't be stingy with compute.

## Derisking

Always start with a samller sample to do end to end "sign of life" tests. Samples reduce the overall dev-loop time[^1] and makes sure that you don't waste time on something that's not going to workout.  Take extreme care to make sure tha sample is not skewed. For instance, training data from before 2016 will perform worse on a test set in 2020 than a more recent training data corpus. 


## Clear hypothesis testing

It's important to tease out different components of changes (a new data source, a different filter, a new html extrator), and have a clear accounting of their individual impact. It's not good enough to just say, hay, this bag of tricks together is better than before, because LLM training is high stakes and people need to know that we are getting the best of all possible approaches and know how different effects will stack.

It's also important to do end to end testing, since the parts may cancel each other out. For instance, better cleaning may initially seem better for the models, but if the cleaning deletes too much data, the overall data reduction may force repetition, which is bad.

Given how many parts there are, I wish I had wrote things down more often and had a clearer plan. Attention to detail is key here, and a detective who reasons perfectly over “facts” they mis-remembered is useless. 

One corollary of this is to keep things simple. Simple logic contains less code (i.e. less bug) and is easier to explain and understand. Data bugs are harder to detect given that they are so open ended, so simplicity helps a lot.

The corollary of this corollary is then to keep ones eyes on the big price. Data work often contains a lot of red herrings. Just because it seems like an idea with a positive outcome doesn't mean it's worth it.


## Don't be stingy with compute

I went out of my way to save compute, which was a big mistake. It ends up being more costly in the end, both of my time and the overall compute. Here are some concrete manifestations:

* Doing derisking and hypothesis testing well both require a lot of extra compute, but they are extremely worth it. Derisking reduces investment into worthless efforts, and clear understanding of the changes brings confidence and clarity to the whole complex system.
* I wrote some caching logic for partial failure recovery. Then I realized that the failures were not even that common---just 5% of load, and the caching logic had some extra code and bugs that slowed me down. In the scheme of things, the caching was totally not worth it.

These are my three lessons!

Ultimately, I decided that running data experiments is a bad fit for me. It reminded me of the time in highschool when I decided that I cannot become a biologist---I was running some polyacrylamide gel images, and each imagining session took me a few days, and for the four times I ran the images, I made a small mistake each time. I felt defeated and unproductive.

These days, I'm back to building tools, which I love!

[^1]: I find that generally, reducing dev loop time is one of the most high leverage time investments for all research engineering.

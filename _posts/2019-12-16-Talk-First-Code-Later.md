---
layout:     post   				    
title:      Talk First, Code Later 
subtitle:   how to code
date:       2019-12-16				
author:     hughnian				
header-img: img/talk-first-1.jpg
catalog: true 						
tags:							
    - Golang
---

An engineer from another office and different timezone raises a pull request for a service/library my team owns. The pull request has little to no description and makes no sense from our perspective. We write a long message explaining why this change can't happen. Then we block the change.

The next day, this other engineer adds some more comments, arguing why the change should go through, after minor modifications. We still disagree. Later, we get an email escalation from their team with a few more people on the thread, asking us to allow the change and unblock them. We are stalling, getting nowhere.

Then someone on our team asks the other engineer: **Why are you making this change? What problem are you trying to solve?** We talk over chat, then do a video call. Suddenly, everything becomes a lot more clear. We've been talking over each other.

The other team had a problem with our system. We had no clue this was the case. This other team then coded a solution to scratch their itch. We follow an internal open source model with strong code ownership, where any engineer can put up pull requests for codebases. The owning team of that system sets up blocking code reviews. This means that at least a member of the core team needs to approve it before it can be merged.

The problem was, the developer proposing the change was focused only on their use case. They also didn't have an in-depth overview of how our system works. They even had less understanding of how to extend the system and all the other customers we need to worry about. Had they told us about the problem, we could have offered them either a workaround or let them know of a feature we were building already, that solves the same category of problems.

**Everyone would have saved so much time, if only we communicated first and wrote code only after.** The developer with the problem spent a lot of time understanding our system, figuring out what changes to make, and making that code change. Yet, with all this work invested, they spent no time describing the "why" of the change or reaching out to our team to get feedback. This was despite there being chat groups, email lists, and how code maintainers are pretty easy to track down.

After we all got on the same page, we made a plan, the other team abandoned their original change, and we agreed on the way forward. In one case, we shipped the new feature that unblocked the other team. In the other case, we coached the other team on how to implement the change in a way that we could accept the pull request.

The takeaways from both cases were the same. **When making changes to a system you are not familiar with, follow these steps to reduce churn:**

- Summarize the problem you're trying to solve. Talk with people owning the system, sharing your problem.
- Share and validate the fix you're thinking of. Reach out to engineers on the owning team: in-person, over chat/email or a call, and talk
- Describe your code changes clearly, following pull request best practices. Especially when working across timezones, you might not be able to verify your fix: a clear description will help in this case.
- Do the above, especially when working with remote teams or across timezones. It's tempting to think, "oh, the owning team is another timezone, I'll just write the code while they sleep." Still, it's far more efficient to shoot an email summarizing the problem and your approach first. If you're in a hurry, you can also write the code and submit a pull request at the same time: you already are offering a lot more context this way. However, in the majority of cases, getting that initial validation can save many revisions of back-and-forth.

It's interesting how problems like this are mostly nonexistent when the whole team is colocated. However, when working across locations, the "talk first, code later" approach is an un-intuitive tool that speeds development up and leads to better communication between engineers and teams. This is despite how it feels like it will slow things down by delaying writing code.
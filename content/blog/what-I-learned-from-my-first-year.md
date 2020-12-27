---
date: "2019-09-13"
title: "What I Learned From My First Year as a Software Developer"
tags: ["Programming", "Teams", "Career"]
draft: false
---

Like a lot of individuals in software development I worked in a different industry prior to making the career shift. I recently completed my first year as a software developer at Cascade Energy and thought I would share a few bits of information that may be of use to others who are beginning to embark on a similar path.

After working 10 years in the accounting field, I acquired an array of skills and experiences that ultimately presented software development as a viable alternative career path. It should be noted that these skills are not specific to accounting; in fact, you may find that they are universal skills for all endeavors. Therefore, I have purposely made light comparisons to my previous career experience for illustration reasons only.

In no particular order:

**Communication is Key**

Being able foster collaboration between team members, external customers, and other stakeholders is a universal skill that is important in all fields.

A concept that I was exposed to a few years ago was the idea of "emotional intelligence". I believe this is a fundamental prerequisite for effective communication and can be summarized as effective communicators should possess traits such as: having empathy for others, being self-aware, and utilizing social skills that inspire others. To read more about emotional intelligence, see Daniel Goleman's article titled _What Makes a Leader_ in the [Harvard Business Review](https://hbr.org/2004/01/what-makes-a-leader).

At Cascade Energy, we are a small team of engineers (fewer than 10 people) who work across the stack on a regular basis. We each have specializations, such as front-end, back-end, infrastructure, etc., but our team structure is such that we have to be able to work in all parts of the product with confidence to ensure that we can meet the milestones in our roadmap.

To facilitate this, we make communication the utmost priority. When picking up a ticket that I may not be familiar with, such as writing an AWS lambda function, I will read the code for similar repos, look over blog posts and consult with more experienced developers on our team before starting. This will ensure that I am able to adequately plan a solution before coding. These processes take a lot time to complete. In fact, I was surprised at how little time we spend _actually_ writing code throughout a typical day - it is a lot less than I thought!

Our stack is written mostly in JavaScript. Luckily, I had focused on front-end development when learning to code and was fairly comfortable writing JavaScript. So, that made working with our Node services slightly less daunting. As a team, we foster an encouraging environment that encourages developers to work on tasks that are outside their comfort zone. So, planning solutions with other team members, pair programming, and other knowledge sharing was instrumental in my personal development.

I learn best by doing. Pairing with other experienced developers allowed me to see how they think and leverage tools to solve problems. Making a habit of doing this early on was key in learning our codebase. Additionally, pair programming is an excellent team builder - solving problems with other people is a lot more rewarding than solving them yourself (and probably takes less time)!

_Side note_ - ultimately, when you land that first programming job, you will not be able to anticipate what stack you will be working in. As long as you are comfortable with one language, you can easily pick up another - the core analytical skills that you acquire while learning your first language remain constant.

**Problem Solving - Have a Plan, Implement It and Leverage Your Team**

Whether it is implementing a new feature, fixing a bug, researching a topic, or sketching out the technical details of an infrastructure ticket, problem solving is inherent in what software developers do each day. Observing problems at a high level allows one to deeply understand the issue, which sets them up to adequately weigh possible solutions.

In practice, the following has worked well for me:

1. Summarize the problem in one paragraph.
2. Using lists, define the following:
   - potential causes of the problem.
   - potential solutions to the problem.
3. For each potential solution, outline the pros and cons.
4. Select a solution and implement it

Once you select a solution, sketch out how you would implement it. This could take the form of words, diagrams, pseudo-code, or whatever best illustrates the particular issue at hand. This document will serve as your roadmap for the code you will eventually write. Writing code in accordance with an outline is much more effective than writing code without one. You will be able to better track your progress and have an easier time debugging issues along the way. Besides, the code is basically already been written - you are just need to translate your outline to your programming language.

**Debugging is Awesome**

Learning how to properly use debugging tools has had the most profound impact on my programming skills. When I first started working as a software developer, my debugging strategy was limited to placing logging statements throughout the codebase and hoping that I would find clues as to the location of the problem.

Logging statements are fine, but they should not be the only tool in your toolbox. Once I was introduced to the debugger in my browser and text editor, my understanding of our codebase increased by an order of magnitude.

Debugger tools allow you to place breakpoints strategically and see what variables and functions (including arguments) resolve to. Further, in addition to the values that are in scope with respect to your breakpoints, you can see all of the values throughout your program for a more holistic view of how the code is executing on each call. That way you can understand _what_ and _why_ the variables and functions in your program are resolving in the manner they are. The combination of logging statements and debugging tools has enabled me to learn new code faster and therefore allow me to fix bugs and write features a lot more effectively.

**Summary**

As you can see, the themes of communication and problem solving (debugging included) are key attributes for a software developer to possess. The best part is, however, that these skills are required in many facets in life and are not specific to software development (or accounting!). That means you get to bring _your_ specific life experience to your team to leverage collectively. The diversity of life experience within a team enables it to make better decisions because the team has access to different perspectives. After all, as software engineers, we determine solutions to address problems for our customers, so we better do it the best way we can!

---
layout: post
title: "How to start a machine learning project properly?"
date: 2022-08-28 18:00:00 +0100
tags: machine learning ml machine-learning python project start
---
![image](/assets/images/rocket.png "starting a project"){: width="200" style="display:block; margin-left:auto; margin-right:auto"}\
<span>image: <a href="https://www.flaticon.com/free-icons/rocket" title="rocket icons">Rocket icons created by Freepik - Flaticon</a></span>{:style="display:table; margin-left:auto; margin-right:auto; font-size:12px;"}

Machine learning is getting more and more popular nowadays. Companies are creating or extending their R&D teams. Having a product that delivers some kind of AI solution is prestigious.

In terms of software development, building business web applications is a quite mature field. There are well-known practices and methodologies on how to plan, design, and implement a solution. But AI is quite a greenfield and when it comes to building AI solutions, it's a big challenge how to do it well.

Machine learning and in general AI are very fast-paced in terms of development. The SOTA (State Of The Art) models and solutions are changing rapidly, and new tools and libraries are being released.

Business people are often not aware of what is possible, and where are the limitations of AI solutions, they might be driven by AI hype from clickbait news. Data scientists might be excellent in terms of machine learning knowledge and statistics but they might lack project planning skills and they might not be able to deliver large-scale production-grade solutions. Machine learning engineers should be somewhere between software engineers, data engineers, DevOps, and data scientists to be able to support data scientists with developing well-crafted AI solutions for production - but they might be more focused on making an interesting project in terms of their development, and not optimally meeting business requirements. Project managers might have trouble with understand well project risks because it requires some prior machine learning knowledge.

The things described above might be some of the challenges, but there is one, probably the biggest of them. Machine learning and AI have an experimental nature. It's almost impossible to tell if the designed solutions will work in practice or not. It requires experiments, and usually a lot of iterations on the whole process of collecting the data, cleaning and preprocessing, and model training.

Luckily, those problems are common in this area and a lot more people have occurred them. There are some good practices, how to start a machine learning properly, what are the most important points to define, and in general, how to approach developing an AI solution.

#### Machine Learning Project Checklist:
- what is the goal?
- what is the metric of success?
- what is the minimum value of the metric of success to go live with the solution?
- can it be solved with a non-machine learning solution?
- what is a better way to solve this problem (machine learning or non-machine learning solution) and why?

*assuming the decision is to go with the machine learning approach*

- what data will be used?
- what is the cost of acquiring/labeling the data?
- what will be a baseline model?
- what are the risks of the project?
- what are the unknowns?
- can this problem be simplified and how?

It's good to address those questions while starting a machine learning project. The business should discuss that with the development team and they should together come up with the answers to those questions. It's worth spending more time on project planning than starting without a clear vision and some misunderstanding between business and an AI development team - to deliver in a few months solution that... cannot be used in production because it lacks some features or is not good enough.

The Machine Learning Project Checklist was created based on my own experience and on the [Machine Learning Engineering by Andriy Burkov](http://mlebook.com/) which I greatly recommend reading! :)

If you have any more ideas about starting a machine learning project, I greatly encourage you to put them in the comments section below :)
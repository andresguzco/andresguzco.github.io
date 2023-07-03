---
title: Suburbanites
subtitle: Analysing the Evolution of Commuting Dynamics in The Netherlands

# Summary for listings and search engines
summary: This page is a journal of my development process of the paper 'Suburbanites'

# Link this post with a project
projects: []

# Date published
date: '2022-06-24T00:00:00Z'

# Date updated
lastmod: '2022-06-24T00:00:00Z'

# Is this an unpublished draft?
draft: True

# Show this page in the Featured widget?
featured: false

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: ''
  focal_point: ''
  placement: 2
  preview_only: false

authors:
  - admin

tags:
  - Academic
  - Urban Economics

categories:
  - Working Papers

## Overview
---
This project started on the first week of June 2022. I had to develop a proper research paper on a teams of 3-4 
students. While this sounded exciting, I already had experience writing scientific papers from my medical school times, therefore I had the idea to do a paper on my own. I understood that it would involve a significantly higher workload because the paper was due in less that 4 weeks. Nonetheless, I always like a challenge so I decided to walk down this path. After proposing this to my supervisor, which happened to be also my supervisor at the Research Assistantship, he agreed and encouraged me to undertake this upcoming project. To explain where the idea was born, I have to backtrack and give some insight on my work at the Spatial Economics Department from the Vrije Universiteit Amsterdam, my alma mater.

I was captivated by Regional and Urban Economics during the Fall semester of 2021, I found the incorporation of the spatial aspect, with emphasis on distance, a very interesting addition to economic analysis. Economics tends to dispense of many of the real-world variables by making some reasonable yet oversimplifying assumptions in order to make modelling the phenomena feasible. While undergraduate Economics is taught in this fashion, most fields of graduate Economics have become 'harder' in the last decades, now it's almost necessary to be acquainted with Real Analysis, Ordinary and Differential Equations, and rigorous Probability and Statistics to pursue a research career. This simple yet beautiful addition of spatial variables to models, sparked in my mind many questions and ideas. I was even more intrigued when we started learning about the application of the gravity model to international trade and migration, what I later discovered to be a part of Econophysics. It wasn't long before I was approaching my Professor to ask for RA opportunities with him, especially since I was already decided on pursuing an academic career in Economics (the main reason behind the existence of this website).

One of the very first topics that we started working on, was the analysis of commuting flows. These flows could be modelled with the aforementioned Gravity model to analyse the distance decay on agent's residence-work location decision. Adding to this, we were located in a country to gather plenty of data for this type of analysis. Therefore, with the Research Paper approaching, I decided to write it about this topic.

While we will go through the process of what has been done, I will summarise these past steps to start focusing on the current stages of the project and how it is progressing past its course's outcome version. First, I gathered the data from the Centraal Planbureau voor de Statistiek (CBS), the data set included to commuting flow between municipalities from 2014 to 2021. Immediately, I ran into one of the biggest hurdles of the research, and one of the main hurdles for any applied researcher. The dataset was anything but ready to be used in the analysis, not insofar as to just scale the variables and move on but it was rather chaos. To my 'delight' the Netherlands had been merging municipalities for the last 7 years, which raised two issues: I had to figure out an approach for the data wrangling given that I cannot analyse the flow of non existent municipalities in the Netherlands, and I had to come up with ways to analyse if this merging could be distorting the evolution of the commuting dynamics.

The first task was rather easy to solve due to my supervisor's wise advice. For the first analysis, I had to merge the municipalities back in time to only have the current 355 municipalities that exist in the Netherlands, owing to the fact that there has only been mergers and no separations. Therefore, it was a task that involved many hours of coding, structuring and debugging to prepare a data set that had a $355x355$ symmetric matrix for each year.

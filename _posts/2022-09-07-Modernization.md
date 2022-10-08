---
title: "Modernization Project"
layout: posts
---

# The path to modernizing a legacy platform

## Background:

At IBM, I worked as a software engineer, specializing in z/OS. In brief, z/OS is an operating system that facilitates most of the banking transactions around the world due to its reliability, availability, and serviceability. My role consisted of diagnosisng customer problems that ranged from a warning message to outages that were affecting their SLA requirements and costing millions of dollars. 

My "day job" consisted of analyzing documentation (memory dumps that were collected by the system, logs, etc.) and analzying our code to find the area that was causing the problem. In this post, I'll focus on how I went about enhancing the engineering experience on the documentation side. 

## Transitioning to the web

When I first started my job, there was only one way to look at documentation, through a platform called IPCS. This platform was a black and green terminal that didn't have significant improvements since the 90s. The defaults for scrolling were F7/F8 and control was your "enter" key. While the platform did not have the GUI features that I was accustomed to, it definetly met the requirements of the job. However, the biggest drawback to the platform was the ability to collect data and training of new hires. 

In 2020, the Covid-19 pandemic gave my colleagues and I an opportunity to to start new projects with many of us saving time on commute and less meetings. The idea was to leverage REST APIs provided by z/OS and create a front-end web facing client to allow engineers to debug on a familiar platform. The data will be presented on a website vs. a terminal where users can use control-C or control-V to copy and paste or use their mouse to scroll up and down. 

While the concept seems easy, the technology behind it was a bit of a challenge. The REST APIs weren't made for our use case, so we had to find clever ways to getting the data and work around the limitations of the API. Furthermore, browsers are bad at dealing with millions of lines that are returned from the backend and that had to be optimized so that the user wasn't spending all their time waiting for the website to load. 

In conclusion, we had an Alpha version that recently got sponsored users. We now have big banks that partnered with us to deploy a Beta version on their systems to try out.








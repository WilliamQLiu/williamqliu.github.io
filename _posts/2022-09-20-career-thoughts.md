---
layout: post
title: Career Thoughts
---


# {{ page.title }}

I'm just adding some random career thoughts after a few years as a programmer.
Just want to say, my opinions are my own and not the views of my employer.

## Advice for Newer Software Engineers

Before the Job

* Your first job is the toughest one (to get); the process is much easier after you have a year or two of experience
* Grind out [neetcode](https://neetcode.io/) for the technical interviews

During the Job

* Avoid taking on [Glue Work](https://noidea.dog/glue)
* Focus on __promotable work__ for your level; use your 1:1 time with your manager to drill in on what that is
* Know what your learning style is (e.g. if you prefer frequent pairings, digging into a problem on your own)
* Try to use data/metrics as much as possible to help make decisions / help others make decisions
* Focus on highest impact work

Example 1 (excel at your core job functionalities first):

I was talking to a newer engineer that wanted to improve the team's onboarding process.
No one stopped him from updating documentation, but I doubt that was __promotable work__
since it was difficult to measure the impact of documentation and he didn't have much code to show.
You need to cover your core skills first for your level (e.g. L1 == coding for a software engineer).

Example 2 (choose your own project):

We currently have a project that's been running 2 months with 3 engineers and is still months from
finishing. The app only has about 20 daily users with about 30 sessions a day. The main impact
is we don't have to run an operational task (that takes 10 minutes after business hours) once every 30 days.
It's low impact work so even if you did a great job technically, it doesn't mean much; avoid that
project and propose higher impact projects that the team can work on instead.

Switching Jobs

* Switching jobs will increase your salary/recognition the quickest, but doing it too often (e.g. every year) is suspicious
* I find there to be a lot of satisfaction in being able to solve difficult problems (that you can only see after being at a place for a longer time)
* The two points above are conflicting, but companies bank that you won't take the time to interview
* It's much harder moving up in levels within a company than just swapping companies (placing a level is hard, try not to get underleveled)

## Advice for More Experienced Engineers

* Pay it Forward and help Newer Engineers, whether that's offering to help pair, listening, or giving advice based on your experience
* Find out what [development level](https://williamqliu.github.io/2022/08/16/situational-leadership.html) they are for that subject so you can help offer the right amount of support
* Look into System Design Interviews when switching jobs

## Current Project

Problem:
We have a RDS table 'history' that tracks every change data capture on the entire database.
That means Table A or B makes an update, we write that update to the 'history' table.
Our database suffers from brownouts because of the high load on this table (e.g. 50% of writes)

Solution:
Moved the 'history' table outside of the RDS using Debezium (kafka connect with a mysql source connector)
that writes to Kafka topics (1 table == 1 topic). Created data pipeline to make the data from kafka queryable
by having another Kafka Connect (Kafka Source to S3 Sink), a Lambda tranform to another S3 Sink, and Athena on top

## Toughest Project

Probably due to recency bias, but the project before my current project was basically Google Analytics (i.e. Event Tracking),
e.g. you click on a button, it fires an event saying you clicked on X button and use that data to determine user behavior.

The problem was that even naming events is tough. Fixing it when you've been doing it wrong (no plan)
for multiple years (e.g. people named events 'ButtonClick', 'ClickedButton', 'ButtonClicked' with different
properties / no standards) will take a while (e.g. working across multi-disciplinary teams including product
engineering, business intelligence, data science, data platform, etc) and a lot of alignment. Leadership thinks that
the company is a startup and can fix issues in weeks, but doesn't realize that it's a cultural problem as well as a technical problem
that will take years to fix for a $XX Billion company (i.e. not a startup anymore).

When teams work on a feature, some don't think "What's a way
I can track if this is successful or not; how will I know if I tune X or Y know if it's helping or making it worse?".
Without that data, you're relying on really lagging indicators (e.g. people quitting your app).

The teams that do think about data, the next question is "How do I do this?". This requires a LOT of alignment,
especially if teams are working across different segments of the product.


---
layout : post
title : "Bradley's Java Programming Blog Site Launches"
date : 2016-11-14
---

## Introduction
To help me understand new and existing Java Frameworks, I created this blog to document my journey.  In 2010, I went back to school to complete my Bachelors and Masters in Computer Science.  After I graduated with my Masters in Computer Science in the fall of 2015, I quickly realized that much of my experience was out of date - Servlets and JSP.  As I saught employment, most companies were looking for Spring/Hibernate or Full Stack Developers.  I had little experience with Javascript, and I had even less with Spring and Hibernate.

## Spring Framework
A few months ago I started tinkering with Spring Boot and MVC, and I instantly gravitated towards it.  It was a refreshingly, new experience to create Web Services / MVC applications than I was accustomed to using Servlets and JSP.  One of the first projects I tried to convert was my group Software Engineering project from my Bachelors program.

### Quintessential Program for Any Language
[Spring Framework](http://www.spring.io) has a cornucopia of information regarding to Spring, and I highly recommend this valuable resource to any aspiring Software Engineer / Developer interested in Spring.  Their [Guides](http://spring.io/guides) provide enough information for you to grasp the basics and propel you to learn more.

"Hello World."  Two words that spark imagination for any developer learning a new language.  These two words was a gateway for learning many languages throughout my career.  And, like every language I learnt in the past, I created an introductory Spring Boot and MVC applications for discovery and knowledge.  But, like many "Hello World" programs, you are eager to learn and experience more.  I quickly found myself seeking to expand my knowledge and discover more of what Spring had to offer.

### University Team Software Engineering Team Project (2012)
Our group consisted of seven students; and each student had a particular role as well as coding responsibilities relating to the project.  For our group assignment, after much deliberation, we decided to use Servlets and JSP to construct our site to mimic an online airline reservation system.  The mistake I made dismissing it too fast without researching it.  Most of the students in my group did not have experience with web development, so they deferred many decisions to me - I was the Project Manager as well as Lead Developer on the project.  I based my decision on my prior experiences with Spring, but I didn't realize how the ecosystem has changed through the years.  When we were designing our system, I felt that we were re-inventing the wheel many times.  While this provided us with valuable experience, this cost us time and didn't allow us to benefit from existing, more mature frameworks, who experienced many of the challenges that we encountered throughout our project.

#### Migration
First, I had to create an Index Controller, and I had to configure Spring to render the JSP / JSTL pages.  In the application.properties file, I stipulated the location of the Views - in this case, JSP.

```
### application.properties

spring.mvc.view.prefix: /WEB-INF/jsp/
spring.mvc.view.suffix: .jsp
```

In our project, we had many servlets, so I began with converting each servlet into a Controller.  


## Conclusion
So, with this site, I am trying new frameworks and applications to spearhead my growth using [Jekyll](http://jekyllrb.com) and Markdown to gather my thoughts and showcase my journey to the world.

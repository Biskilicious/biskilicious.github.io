---
layout: post
title:  "Starting Over - Developing a new Platform in Django"
date:   2018-07-11 13:16:00 -0700
categories: Startup
---

In light of a recent layoff, I have decided to pursue the dream of starting my own startup company. So, what originally started as a resource to capture some project work that I had completed, I will now use this as a development diary for maintaining a log of the development steps that I have taken. 

I have already worked through the various steps of installing and configuring Django, as well as configuring Django for login, registration and a few other tasks. So, picking up from there, I will focus on some activities like getting Django authentication working against Google, Facebook, Twitter, etc. 

Last Year, I started an application aimed at providing Cannabis businesses an opportunity to get an insurance quote online by providing their information to the app. This seems pretty straightforward, but between rating policies and all of the factors that go into an insurance platform, it proved to be quite difficult. Instead of focusing on a single niche, cannabis, for the platform, I am going to start over and build the platform so that other niches can be integrated without major rework. This intent of this post is to try to explain the dynamics of the new platform. 

I am choosing Django because it is the most familiar to me. My goal is to build a platform that I can begin to use, but recognize that it will require rebuilding and retooling in order to scale. The intent is to build something that I can use to make revenue in order to hire the development team to help cross that bridge when it comes. 

In current insurance models, especially for those of the niche markets, the prospective customer searches the internet for insurance providers who offer the type of insurance in which they are searching. When they find the one or two providers, they are given the option to complete a form, which is then emailed to the insurance agent, or they pick up the phone and call the agent. The issue is that the sites of these agents will indicate that they offer an online quote, but the quote is really to fill out a form, which is then emailed to the agent, who calls the prospect with the quote. 

This platform will be different in that the prospect will fill out a questionnaire and receive a "ballpark" quote. If they choose to continue, they will be presented with an extended questionnaire that will be used as the basis to complete the application(s). The applications are unique and custom for each of the downstream carriers, therefore, the data will be stored and each carrier for each niche will have a template that is specific to their custom application. I don't see a way around this customization, but these don't usually change frequently, so it should be ok. As the proposals are returned to the agent, they information is fed into the system for each application. After all of the proposals from the carriers are received, the platform will email the customer, directing them to the platform where each carriers' limits and costs will be displayed. Finally, the customer will be able to select a proposal and purchase the policy online, which will trigger an email to the agent and carrier informing them of them purchase and binding the customer. 

Next phases will be to allow for the download of policy documents, certificates of insurance, submitting and processing claims, renewals, and other aspects of the business of insurance. 

This is a lean startup, in that I am programming this myself and will bring the application to a level where we can start selling policies. Once revenue comes in from the policies, I will reinvest that revenue to enhance the application and add the above features. The ultimate goal is to provide a 100% customer operated insurance application, where the role of the agent is to guide and monitor (which can be done at any time and from anywhere!).

As of now, I have built the first iteration of the platform with the ability to capture the questionnaire, generate the quote, generate the proposal, complete the questionnaire, and fill in the Word Document version of one of the carrier's applications. I was recently successful in using Django to populate the PDF versions, but this method is a HUGE hassle. I am going to try a different approach, where I render the application in HTML for review, and if it is good, convert it to a PDF document that is not a form...just a PDF.
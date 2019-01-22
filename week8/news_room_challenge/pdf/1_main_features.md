## Introduction

You are challenged to build an end-to-end news creation, production, curation, distribution and publishing platform for localized news.It is up to you to dive into this domain and get to know as much as you can about how news is produced, selected and published. The key to a successful project is to plan ahead \(Just Enough Up-front Design vs Complete Up-front Design\) and capture the requirements. This is not an easy task and will require a lot of assumptions and decisions on how to prioritize your features and work.

## Main features

What follows is a general overview of the functionality our client, a Media Conglomerate has provided for us. As a brief submitted by a non-technical client, the brief is in no way complete and require you, the Agile Team, to make decisions on your own.

#### Location

We want the system to be used in editions based on geographical locations. News can appear in one or several editions, depending on their validity for readers \(location, language, etc\).

#### News

It's alla bout interesting and engaging content, right? Don't forget that readers enjoy images alongside text. So make sure you can add one or more images to each article.

#### Categories - Sections

News should belong to a category \(i. e. Sports, Politics, Economy, World, Entertainment, etc\).

#### Authors

Every news article can be attributed to one ore several authors/journalists

#### Rating

Every news article can be rated using rating system \(1 - 5\) with a possibility to add an optional comment.

#### Monetization

\(This part, related to the business side, is to be decided by each team without much guidance from the coaches. Please be prepared to argument your decisions\)

* The publication could implement restrictions to non-subscribing readers.
* Readers can opt in for a 6 or 12 months subscription.
* Revenues can come from sales of ads

#### Administration/moderation

Each news article will be written by an Author/Jounalist and need to be approved by the Editor/Pubicist before being published at a specific date.

If the publication is running ads, they also need to be moderated and approved.

#### Multiple language support

The Media Company that is our simulated client, operates on several markets and would like the platform to be able to support multi language support. This means that ever news article should be tagged with a language attribute and filtered thereafter. The entire UI should also be translated into at least 2 languages.

#### User roles

The platform should support multiple user roles that are authorized to perform different actions. Some suggestions are:

**Editor/Publicist** - Can perform all CRUD actions on all objects in the system

**Journalist** - Can create news articles with associated objects \(see below\)

**Subscriber** - Can have unlimited access to the entire publication and leave ratings and comments.

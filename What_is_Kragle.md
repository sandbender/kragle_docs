# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Next Topic: How it works](./How_it_works.md)

## What is Kragle?

Kragle is a service that lets you automate actions between sites, services and apps.

It is the "glue" which you use to stack these actions together, and it runs the resulting
stacks of actions for you on a recurring schedule, 24/7/365.

Kragle requires no programming knowledge at all - you can use our website to setup all
your automatic app Kung-Fu and be up and running in minutes.

### Okay that sounds pretty sweet, but... give me some examples?

In Kragle a set of actions that you've glued together and setup to work automatically
is called a Stack. Here are some Stack examples..

- **Tweet** when you favourite a *Track* on **Soundcloud**
- Automatically copy **Pinterest** *pins* to an **Evernote** *notebook*
- Add an *event* to **Google Calendar** when you clip a *recipe* on **Epicurious**
- Add items to an *rss feed* when there are new results for a *search* on **ThePirateBay**
- Send a *message* to a **HipChat** room when a new *commit* happens on your **GitHub** account

#### Stacks are **not** limited to just two actions

- use Kragle to post a *message* to **LinkedIn, Facebook and Twitter** all at the same time
- copy an **Epicurious** *recipe* to an **Evernote** *notebook* when you favourite it, and  
  then create a **Google Calendar** *event* for dinner tonight with a *link* to the recipe

#### Simple Filtering/Processing

- Watch for new *front-page items* on the HackerNews **rss feed**, **select** only items matching  
  a *pattern*, and then send those *items* to your **Pocket** queue.
- Search **recent tweets** for a *hashtag*, **filter** any that don't ALSO have another *hashtag*,  
  and **retweet** the results **at a specific time**.

#### Schedules (How often do my stacks run?)

- Stacks on a recurring basis by default (every 5 or 15 minutes, etc)
- Stacks can be setup to run at a certain time instead of every X minutes
- Stacks can also be setup to run only one, at a specific time on a certain day
- Stacks can be configured to **not** run automatically, but instead can be
  be run directly by you OR as a url that only runs when accessed

#### Am I limited to using only the sites/services for which you already have support?
##### Can I connect Kragle to my own website/service/app/server/etc, or a 3rd party service which isn't already listed on your site?

No, and Yes, respectively :) While it requires some technical knowledge, **any** user is able
to add support for new sites/apps/etc with **no interaction required** from the Kragle team.
Our system supports this feature natively, and was a core requirement when we initially built
Kragle - we want the entire system to be as flexible as possible and to encourage the
community to add new action types and share them with others, etc.

The only time Kragle involvement is required is if/when a site or service requires some form
of authorization (ie: logging in/granting permission/etc) for the operation a Kragle stack is
going to perform. In this situation, we are automatically notified that a new action type has
been created which requires authentication, and we will immediately add the appropriate support.

(*Technical details: Simple auth types are already built-in, so if for example the service you
with to connect to uses HTTP Basic authentication, this is already available and configurable
by an end-user when creating a new action type.

For more complicated scenarios, we have pluggable auth support and an extensive system for
adding OAuth support for new services. What this means is that in most cases, if you add a new
action that talks to a site requiring OAuth or some other non-trivial authentication, we will
add support for it immediately (as soon as we're notified that new auth support is required,
which will happen automatically). Adding the new support, due to the pluggable auth system in
use on our back-end, can typically be done in less than an hour (usually **much** less), and our
commitment is to complete any new auth support in less than 24 hours from the time we're
notified*)

##### [Next Topic: How it works](./How_it_works.md)

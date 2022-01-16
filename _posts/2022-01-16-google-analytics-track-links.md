---
published: true
toc: true
comments: true
layout: post
title: Tracking Link Clicks With Google Analytics
description: Monitor clicks to specific links on your website (outgoing, affiliate, ads, etc.) using Google Analytics GA4.
image: images/logos/google-analytics.png
categories: [google analytics]
---

[Google Analytics](https://marketingplatform.google.com/about/analytics/) has been very helpful to monitor and analyze web traffic on my blog. It provides useful insights into how frequently which of my posts and pages are visited, where visitors are from, etc. As such it already keeps track of link clicks to and within pages of my blog.

However, it is often also interesting to track and monitor clicks to outgoing links, i.e., to external websites. This is useful, for example, to keep track of clicks to affiliate links on a website. Such outgoing link clicks are not tracked and shown by default in Google Analytics (the new/current GA4 version) but have to be configured manually.

It took me surprisingly long to figure out how to track clicks to specific (outgoing) links in Google Analytics, even though it is very simple. To spare others some time, here how it works (details and screenshots below):

* Create a new event and define which links you are interested in by using the `link_url` parameter
* Optionally, mark the new event as conversion, e.g., for clicks to affiliate links or ads
* After a few days, Google Analytics will show the day-by-day clicks (and conversions) to the configured links

# Creating an Event to Track Link Clicks

Google Analytics monitors a variety of different events such as clicks on the website but also how often users scroll down on the website, how often they download files, etc. You can also define your own custom events to track, which allows tracking clicks to specific links.

To create a new event for tracking link clicks, go the the [Google Analytics dashboard](https://analytics.google.com/analytics/web/). On the left side, click "Configure" (left), which shows an overview of the existing events and a button to create a new event:

<figure>
    <source srcset="{{ site.baseurl }}/images/ga-track-links/ga-create-event.webp" type="image/webp">
    <source srcset="{{ site.baseurl }}/images/ga-track-links/ga-create-event.png" type="image/png">
    <img src="{{ site.baseurl }}/images/ga-track-links/ga-create-event.webp" width="948" height="320" alt="Overview of Google Analytics events." />
</figure>

Click the "Create event" button, which shows any previously created custom events. To create a new event, again click "Create". You should now see the form for configuring the new event:

<figure>
    <source srcset="{{ site.baseurl }}/images/ga-track-links/ga-config-event.webp" type="image/webp">
    <source srcset="{{ site.baseurl }}/images/ga-track-links/ga-config-event.png" type="image/png">
    <img src="{{ site.baseurl }}/images/ga-track-links/ga-config-event.webp" width="1222" height="605" alt="Configuring a new event on Google Analytics." />
</figure>

First, let's give the new event a name. This should reflect which kind of link clicks the event corresponds to. Also, the name should be in lowercase and without spaces. E.g., for tracking link clicks to https://www.example.com/, you could name the event `click_link_example`.  

Now, the most important part is to define the matching conditions for the new event. For tracking link clicks, use the `link_url` parameter. You can use the event with this parameter to track clicks to a single, specific link or to groups of links.

## Tracking a Single, Specific Link

If you want to track a single, specific link, e.g., https://www.example.com/abc, set a single matching condition with parameter `link_url` and operator "equals (ignore case)".  Then set the value to the specific URL of the link, e.g., `https://www.example.com/abc`.

## Tracking Groups of Links

If you are interested in clicks to all links going to a certain website, but you do not want to distinguish clicks to different pages on that website, you can create and track a single event for all these link clicks. For example, you want to track clicks to https://www.example.com/, https://www.example.com/abc, https://www.example.com/def, etc. all together in a single event.

For that, you also just need a single matching condition, again using the `link_url` parameter. Here, simply use the operator "contains (ignore case)" instead of "equals (ignore case)" and use as value the domain name (e.g., `example.com`), not the full URL (e.g., `https://www.example.com/abc`).

I use this approach, creating one event per external page that I am interested in, grouping all links to that page in a single event.

## Tracking Affiliate Links

It is also simple to add additional matching conditions for more fine-grained control. For example, this allows tracking clicks to affiliate links, which typically refer to one or multiple pages on a partner website and always contain a certain suffix holding the affiliate ID, e.g., `?in=569`. Of course, this affiliate ID is specific to you and the website.

To track clicks to all affiliate links, simply extend the approach from above:

* Use one matching rule to capture all link clicks to pages of a certain domain with parameter `link_url`, operator "contains (ignore case)", and value corresponding to the target domain (e.g., `example.com`).
* Add a second matching rule (click "Add condition") that filters out only affiliate links to the above domain. For that, use the parameter `link_url`, operator "contains (ignore)", and use your affiliate ID as value (e.g., `?in=569`).

<figure>
    <source srcset="{{ site.baseurl }}/images/ga-track-links/ga-config-event-filled.webp" type="image/webp">
    <source srcset="{{ site.baseurl }}/images/ga-track-links/ga-config-event-filled.png" type="image/png">
    <img src="{{ site.baseurl }}/images/ga-track-links/ga-config-event-filled.webp" width="947" height="495" alt="Configuring a new event on Google Analytics; filled form." />
    <figcaption>Configuration of a new Google Analytics event for tracking link clicks to example.com with an example affiliate ID</figcaption>
</figure>



Finally, click "Create" to save the newly create event - whether it is for tracking a single link, multiple links, or affiliate links. Note that the new event is often not directly included in the list of events. For me, it took several days until Google Analytics showed the new event - both in the reports and in the list of events under "Configure".

# Conversions

Conversions are events when visitors turn to paying customers. In Google Analytics, you can define yourself which link clicks (or other events) you count as conversions, e.g., affiliate link clicks. Google Analytics then provides extra reports on these conversion events.

To mark a new event as conversion, simply go to "Configure" in the Google Analytics dashboard, and select "Mark as conversion" in the right column for the specific event. Note, that newly created events are not yet listed there and it takes some days until they are listed.

If you do not want to wait, you can also directly mark your new event as conversion, even before it is included in the list of events. For that, click "Configure", then "Conversions", then the button "New conversion event". Now, copy and paste the exact name of the new event into the field.



Google Analytics will start tracking your newly created events and monitor clicks to the defined links. After a few days, your Google Analytics dashboard should show the link clicks under "Reports > Engagement > Events". If you defined conversion events, these should be visible under "Reports > Engagement > Conversions".

I hope this was helpful. Let me know in the comments if anything remains unclear.

# What Next

* [Adding Google Analytics to a Django App](https://stefanbschneider.github.io/blog/django-google-analytics)
* [All blog posts on Google Analytics](https://stefanbschneider.github.io/blog/categories/#google%20analytics)

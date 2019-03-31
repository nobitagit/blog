---
layout: post
title: How to get rid of Evernote's link interstitial
---

If you're an avid Evernote user like me chances are you have been bugged by a very annoying "feature".
Whenever you click on an external link from on of your Notes you will be redirected to a sort of 
confirmation screen that warns you that you are leaving Evernote.

![_config.yml]({{ site.baseurl }}/images/2019-3-31-evernote-screen.png)

Of course I am, that's exactly why I am clicking on a link [clicks on continue].

This gets tedious very quickly when clicking on more than two links in a row.
Apparently I'm not the 
[only](https://discussion.evernote.com/topic/102215-option-to-disable-the-message-you-are-leaving-evernote-on-external-links/)
[one](https://discussion.evernote.com/topic/102214-how-do-i-turn-off-warnings-and-flyovers/) 
who finds this incredibly frustrating.

Since there's no built in way to disable this *feature* here's a quick workaround that will get this
problem fixed once for all.

First thing, add the browser extension [Redirector](https://github.com/einaregilsson/Redirector), available for Firefox,
Chrome and Opera. And then:

- Open its configuration panel and add a new redirect.
- Under "Include pattern" paste `https://www.evernote.com/OutboundRedirect.action?dest=*`.
- Under "Redirect to" type `$1'.
- Make sure "wildcard" is toggled
- Open Advanced options and under "Process matches" select "URL decode".

Here's an overview of everything mentioned above.

![_config.yml]({{ site.baseurl }}/images/2019-3-31-redirector-config.png)

Save and try opening a new link from Evernote. You should now see you browser opening the desired page.

Credit go to [Einar Egilsson](https://github.com/einaregilsson) whose Redirector
avoided the need for me to create an extension to solve an issue that
has been bugging me for months until I decided to fix it one for all.

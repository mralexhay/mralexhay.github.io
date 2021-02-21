---
layout: post
title: Toolbox Pro - Learnings & Earnings
description: A quick look at some of the numbers a year on on from releasing my first app.
category: toolbox pro
summary:  A quick look at some of the numbers a year on from releasing my first app.
---


It's been just over a year since I released my first app, [Toolbox Pro](https://www.toolboxpro.app).

I've always found it interesting to read break-downs by developers that lift the curtain on their indie apps, so I thought I'd write up some of the stats for Toolbox Pro and some observations I've made along the way. 

Here are the TL;DR numbers (after 13 months):

* 📈 104k (free) downloads
* 💰 ~£63k revenue (in-app purchase & tip jar)
* 💷 ~£41k take-home (after Apple's cut)
* 🔓 ~10% of downloaders paid to unlock premium
* ⭐️ 4.6 average rating
* 📝 153 reviews

## The Idea
At WWDC 2019, Apple announced three things that caught my eye:
* [SwiftUI](https://youtu.be/psL_5RIBqnY?t=7598) - a brand new declarative framework for writing UI that looked way easier than UIKit
* [Shortcut Action Parameters](https://developer.apple.com/videos/play/wwdc2019/213/) - allowing third-party developers to design their own actions for Shortcuts
* [Vision Text Recognition](https://developer.apple.com/videos/play/wwdc2019/234/) - an API that allows on-device text recognition

I was already a heavy Shortcuts user and had been hoping to see third-party actions in iOS13. When I saw SwiftUI, I thought it looked fun to try and I thought an app that added an action to Shortcuts that allowed it to pull text from an image would be useful.

I didn't know any Swift at this point, having only tinkered around with AppleScript on my Mac and JavaScript in [Scriptable](https://scriptable.app). I downloaded [Unwrap](https://itunes.apple.com/app/id1440611372) by [Hacking With Swift](https://www.hackingwithswift.com) and started working through the language demos on my commute to and from work. Hacking With Swift is an incredible resource for learning Swift and SwiftUI - Paul's tutorial articles are concise and focussed and I still use them all the time.

After having the bare basics down and having worked through [Apple's SwiftUI tutorials](https://developer.apple.com/tutorials/swiftui/), I started building out my idea and after a couple of weeks of fumbling my way through it, copying code I didn't particularly understand off Stack Overflow and banging my head against the wall with SwiftUI errors, I had a bare bones app with a single shortcut action that accepted an image as input and output out any recognised text.

As I was developing that first build, it became clear to me that each shortcut action was essentially a drag and drop, codeless 'function' that could accept any text or files and spit something useful out the other end.

I started making a list of actions that would be useful in automations. I browsed [r/shortcuts](https://www.reddit.com/r/shortcuts) and noted down everything people had asked whether there was a way to do in Shortcuts or things that were were tricky and required workarounds using JavaScript or entire shortcuts to perform a single action. 

I ended up with a long list including actions like global variables which persist between shortcut runs, performing a calculation from a written expression, creating menu icons, converting song links to different services, checking if the device is connected to the internet, extracting colours from an image and so on.

![Early Beta Build](/assets/tbp-retrospective_1.png){:.small-img}

<span class="marginnote">Early beta build - and yes, it was taken at 01:42</span>

It was the perfect project to learn code because I was building small functions using lots of different frameworks - Core Image filters one day, Vision face recognition the next.

## Taking (Test) Flight

I worked on the app in the evenings and weekends and once I had a bare-minimum app with 33 actions, I wrote [a post](https://www.reddit.com/r/shortcuts/comments/cyi7uj/new_companion_app_for_shortcuts_looking_for_beta/) on r/shortcuts asking for beta testers for my 'Shortcuts companion app'.

This post was when I thought I might have something good - people seemed to respond really well to the idea, with lots of suggestions and positive feedback.

Over the course of developing the app I ended up with around 3,000 beta testers[^footnote]. I kept adding actions and ended up with around 55 in the build I wanted to launch.

Although the app had made it through TestFlight review I wasn't _entirely_ sure the app would be successfully approved in the App Store. I hadn't really seen another app like this where the bulk of the app's functionality lived outside of the app itself, with the main in-app functionality being documentation for the actions and links to example shortcuts.

To my surprise, it passed review first time (after a nervous couple of days) and I launched the app on November 13 2019.

## Pricing & Marketing

There's (obviously) so much more that goes into making an app than just the code. Marketing and pricing strategies require a good deal of skill and experience in themselves and I definitely don't have any expertise in that arena so I really just winged it.

I decided to make the app free to download with a good number of the tools available without payment and the rest locked behind an in-app purchase. There's a natural virality to users building a sharing Shortcuts using Toolbox Pro's actions so I wanted to make sure some of the big, useful features were completely free to use - like Preview (build native, interactive SwiftUI pages in Shortcuts) and global variables.

I set the price to unlock everything at £5.99

I found [this post](https://charliemchapman.com/posts/2019/9/27/marketing-dark-noise/) by Charlie Chapman about marketing his app, [Dark Noise](https://apps.apple.com/app/dark-noise/id1465439395), a very useful starting point for marketing. 

## Reception

My ambition was to try and hit 10,000 downloads, which seemed like an awful lot for a pretty niche little automation app.








## Marketing
* Charlie Chapman's blog post. 
* Posting on Reddit yielded a good number of downloads
* Putting together a press kit and reaching out to press

## Reception
* Expectations - 10k downloads felt ambitious
* new entry, doing something unusual
* stats
* [SSPAI post](https://sspai.com/post/57529)
* [SSPAI best apps](https://sspai.com/post/57983)
* featured (artwork)
* [Macstories](https://www.macstories.net/reviews/toolbox-pro-review-a-must-have-companion-utility-for-shortcuts-power-users/)
* Shortcuts team using TBP
* [Cult Of Mac](https://www.cultofmac.com/665454/toolbox-pro-brings-incredible-new-tricks-to-shortcuts/)
* [Toolbox Pro brings incredible new tricks to Shortcuts](https://www.cultofmac.com/665454/toolbox-pro-brings-incredible-new-tricks-to-shortcuts/)
* Tools & Toys
* Automators & Launched
* Khoi Vinh reported a bug (principal designer at Adobe)
    
## Earnings
* Didn't do any market research, didn't A/B test etc, just went with gut on what felt right and been happy with the results
* totals
* Surprised continues to earn
* Tip jar wasn't worth it

## Learnings
* Decided not to localise - money on the table but would take it from being a fun side project to being hard work and slowed me down
* So much work goes into releasing an app. Not just coding it, building a website, handling support, marketing, designing screenshots and app icons. Structuring a project, scheduling releases. Haven't done any of these in a professional environment with people who know what they're doing so I'm fumbling my way through it.
* Haven't got the hang of compartmentalising smaller updates on different branches to larger feature updates - art to it. Git management as much as anything
* Pushing things into places that maybe they hadn't been designed to go - most apps wouldn't have 130+ siri intents. Binary scan rejected app for intent count. Hit bugs that probably wouldn't affect 99.9% of apps - Shortcuts team have been incredibly helpful and responsive to fixing these types of issues

## What's Next For TBP?
* latest update in the new year once 14.4 is released
* changed structure so I can add more examples more easily
* always taking suggestions for new tools
* look at new frameworks 
* threw a lot of dirt at the wall, now looking at taking popular features and expanding them logically - menu box and potentially Preview

    
[^footnote]: I decided not to impose a limit on TestFlight since I wanted people to use and experiment with it.

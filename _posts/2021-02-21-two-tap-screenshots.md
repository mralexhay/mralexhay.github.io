---
layout: post
title: Two-Tap Screenshots 
description: Creating framed screenshots using Shortcuts.
category: shortcuts
summary:  Creating framed screenshots using Shortcuts.
---

Building apps often requires sharing a lot of screenshots of your app in action.

I've refined the process down to being able to create the framed screenshot you see below with two taps to the back of the phone.

![Framed Screenshot](/assets/two-tap-screenshots_1.png){:.small-img}

This technique uses the Shortcuts app, triggered by a *Back Tap*, to take a screenshot and pass it to the [PicSew](https://apps.apple.com/us/app/picsew-screenshot-stitching/id1208145167) app, which frames it in a device and cleans up the status bar (the time is set to 09:41 and the network and battery bars are set to full).

It is then resized and a free [Toolbox Pro](https://www.toolboxpro.app) shortcut action tags and saves the images to a newly created folder on my Desktop in iCloud.

Let's dig into each step.

## Taking A Screenshot

As of writing, the developer beta of iOS 14.5 includes a new Shortcuts action: **'Take Screenshot'**. 

When triggered it takes a screenshot of the device's current screen and passes that image to the next action (without saving it to the camera roll).

## Framing The Screenshot

To frame the screenshot, I make use of the excellent app PicSew. As well as stitching multiple screenshots to create a long, 'scrollshot' image, this app also lets you frame a single screenshot in a photo-realistic Apple device.

It has support for the [x-callback url scheme](http://x-callback-url.com), which lets it play nicely with automation in Shortcuts.

Here's the callback URL I use:

![PicSew URL Scheme](/assets/two-tap-screenshots_2.png){:.small-img}

You can learn about the different parameters in [PicSew's documentation](https://docs.picsew.app/en/x-callback-url.html).

I use PicSew because I love the way it normalises the status bar to be the same in each image, but if that isn't a big deal to you, you could skip the app and use [Federico Viticci](https://www,twitter.com/viticci's) excellent '[Apple Frames](https://www.macstories.net/ios/shortcuts-corner-apple-frames-for-iphone-12-and-se-get-image-resolution-encode-images-to-base64-and-search-articles-in-reeder/)' shortcut, which does the same kind of framing, right inside Shortcuts.

## Saving The Screenshot

By default, Shortcuts can only save files into the **'Shortcuts'** directory in iCloud Drive. I find this a bit awkward so I use the (free) Bookmarked Folders actions in my app, Toolbox Pro, to save my screenshots directly to dated folders in a **'Screenshots'** folder on my Desktop instead.

In Toolbox Pro, simply add the folder you wish to use in your automations in the **'Folder Bookmarks'** section of the Settings tab.

![Folder Bookmarks](/assets/two-tap-screenshots_3.png){:.small-img}

Then in the shortcut we can use Toolbox Pro's **'Create Folders'** action to create a dated folder for our screenshots and the **'Saved Files To Bookmarked Folders'** action to save the screenshot to the folder we've just created with a coloured Finder label for good measure.

![Saved Bookmarked File](/assets/two-tap-screenshots_4.png){:.small-img}

I actually save *four* screenshots each time the shortcut is run:

* The original, untouched screenshot
* The full-sized framed screenshot as a PNG with transparency
* Another PNG but scaled down to 500 pixels in width
* A full-sized JPEG, overlaid on a grey background (this is better for posting on Twitter since it doesn't display images with an alpha channel very well)

Here's what the final output looks like: 

![Framed Screenshot Output](/assets/two-tap-screenshots_5.png)

You can easily toggle which screenshots you wish to output in the dictionary at the top of the shortcut:

![Preference Dictionary](/assets/two-tap-screenshots_6.png){:.small-img}

## The Shortcut

You can download my **'Framed Screenshot'** shortcut [here](https://www.icloud.com/shortcuts/609eef2261b44414beadbd1d500b6ad8). Make sure you have PicSew & Toolbox Pro installed and a Folder Bookmark with the name **'Desktop'** or it won't run as expected. At the time of writing, it also requires the developer beta of 14.5.

To trigger the shortcut using a double or triple tap to the back of the device, select the shortcut in Settings.app:

*Settings > Accessibility > Touch > Back Tap*

Alternatively, you could trigger it with Siri.

## Going Further

That's a lot of legwork being done for us with two taps but we can go further.

Once the screenshots have been saved, I have [Hazel](https://www.noodlesoft.com) monitoring the screenshots folder for changes.

When it detects a new batch of screenshots, it'll open the 500px versions in [ImageOptim](https://imageoptim.com/mac) to reduce the filesize by about 75% without any noticeable image quality loss.

Here are what those rules look like:

![Hazel Check New Folders Rule](/assets/two-tap-screenshots_8.png)
<span class="marginnote">This checks for new folders and runs the other rule on the contents</span>

![Hazel ImageOptime Rule](/assets/two-tap-screenshots_7.png)

You could also add a rule to automatically delete screenshots after a certain period of time.
---
layout: post
title: Fixing Keyboard Type on macOS
date: 30th June 2020
excerpt: How I fixed Keyboard Type on macOS when the preferences pane had no such option.
---

## Everything was rosy

I recently purchased a new keyboard to use in all scenarios, both across windows
(gaming), and macOS, work (inc. programming).

Everytthing was great for the weeks that followed getting it, fantastic
keyboard, lovely to type on, nice click-clack sounds (<3 cherry mx blue).

![Corsair K95 RGB Platinum XT](/images/corsair-k95-rgb-platinum-xt.jpg)

However on macOS one day last week the tilde `~` had _suddenly_ moved from being to
the right of `left-shift`, to being below `<esc>`.  
This made things tricky given my use of that key for switching between panes,
and also general dir access etc.

The generally documented fix for this is to update the keyboard type using
the keyboard prefs pane, however the button that's documented was not present
for me :(

## The Fix (for me)

The fix was indeed to change the keyboard type, but the way to do that was
different here.  

It looks like macOS was no longer seeing my external keyboard as external,
therefore was taking it's config/mapping from the built-in KB (even though they
were listed separately in kb prefs).  

Also, macOS thought it was ANSI layout, where I wanted ISO.

In order to get the Mac to see it as external again so I could change the type,
I had to remove the plist file for KB prefs

```bash
rm /Library/Preferences/com.apple.keyboardtype.plist
```

And then reboot, this gave me the normal external keyboard type prompt and
allowed me to select the ISO type.  

## I have my tilde back where it belongsi 🍾

Documenting this so that _when_ it happens again, I don't have to remember or go
on a hunt again, hope it can help someone else too though.  

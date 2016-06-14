---
layout: post
title: Automatically renaming multiple files by rule
date: 16th May 2010
---
I recently had the need to rename a bunch of files according to a simple rule.  
In the directory, any files that had a prefix of "Gee_" should have the prefix changed to "Kif_"  
After a little digging I found a simple but quite tasty solution.  
Simply run this at your command line.
```bash
for f in Gee_*; do mv "$f" "Kif_${f#Gee_}"; done
```
Job done. It finds files matching the pattern supplied, then loops through renaming them appropriately.

It's worth noting this isn't recursive so will only complete for files in your current directory.

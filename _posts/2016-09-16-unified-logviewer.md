---
layout: post
title: Introducing the Unified Log-Viewer
description: "Introducing the Unified Log-Viewer"
modified: 2016-09-16T15:27:45-04:00
tags: [taskcluster, logviewer, react]
image:
  feature: log-viewer/log-viewer.png
  credit: 
  creditlink: 
---

The Unified Log Viewer is a new project initiated by [Eli Perelman](http://eliperelman.com/), with the ultimate goal of eventually replacing Treeherder current log viewer. I interviewed Mozillians this summer asking them about their workflow and I received multiple complaints regarding the current log viewer. See interview results [here](https://public.etherpad-mozilla.org/p/taskcluster-dashboard-interviews). For example, the browser would crash when presented with a very big log file and users are not able to jump to a line number in the log.
The new log viewer project is currently being hosted on [Taskcluster Github](https://github.com/taskcluster/unified-logviewer). We are always open for the public contributions. Send us a pull-request.



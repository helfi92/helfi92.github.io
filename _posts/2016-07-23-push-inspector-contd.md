---
layout: post
title: Push Inspector Update
description: "Push Inspector Update"
modified: 2016-07-01T15:27:45-04:00
tags: [taskcluster, pushinspector, react]
image:
  feature: push-inspector/coffee-phone.jpeg
  credit:
  creditlink:
---

Whenever a Mozilla employee or contributor pushes a build to a server, they go to the Treeherder interface to see and inspect the results of their build. The TaskCluster team decided to come up with a “developer-focused” interface, Push Inspector, a complete rework of Task Group Inspector. Push Inspector is a great way to inspect your builds in real time.

If this is your first time hearing about Push Inspector, I urge you to read the
[previous post](../push-inspector) explaining the project in detail. This post will serve as an update to the latter.

###	Update

Back when I was interviewing contributors about their experience with Treeherder, a significant amount said they wish they would somehow be notified when a build is done. The current workflow requires them to periodically check the website and this affected their productivity. That's why I integrated web notifications that would send a message to users when a build is done or when a task fails.

![Push Inspector](/assets/images/push-inspector/push-inspector.png)

There has been some minor changes to the UI but major changes are planned to come once the UX contractor finalizes the design. Below is the new bootstrap theme design (not final) wherein the [tools](https://tools.taskcluster.net/) website might incorporate.

![UX new design](/assets/images/push-inspector/ux-new-design.png)

Push Inspector is in the process of being merged to [https://tools.taskcluster.net/](https://tools.taskcluster.net/) . If you have any questions or recommendations, feel free to shoot me a message.
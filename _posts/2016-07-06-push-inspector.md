---
layout: post
title: Task Group Inspector Rework
description: "Demo post displaying the various ways of highlighting code in Markdown."
modified: 2016-06-01T15:27:45-04:00
tags: [taskcluster, pushinspector, javascript]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

Push Inspector

### Highlighted Code Blocks
Whenever a Mozilla employee or contributor pushes a build to a server, they go to treeherder interface to see and inspect the results of their build. The TaskCluster team decided to come up with a "developer-focused" treeherder interface and was later called Task Group Inspector. Figure 1 is a visual representation of the interface as of today's post date. The Inspector does a decent job inspecting a build, however it's still not quite there yet. Let's discuss what it lacks.

### Architecture
Task Group Inspector does not use Redux in its implementation. This is not preferred because it makes data attached to the views. The idea case, which Redux provides, is to seperate data from the presentation layer, that is the views.

### Routing
The current interface handles routing with no powerful routing library which opens room for errors and complexity as the hash portion of the URL changes.

### Filtering
When a build consists of tasks approaching a thousand and there is no way finding out that one failing task unless scrolling the full list, it quickly starts to irritate the user. This is the case with Task Group Inspector.

### Realtime
There is a web listener that listens to messages relating to tasks. For example, a message can say something like it went from a `running` state to `failed`. Presently, the user interface does not update the changes in real time. In the contrary, it waits around a minute and then refreshes the pages. This is definitely not the ideal case wanted.

### Data visualization
The only way to currently know the inspection status of the tasks is by scrolling down the list of tasks and checking manually. The UI is lacking a figure showing the status of the build.

All of the above gave rise to Push Inspector, a tool that brought inspecting task groups to another level. What makes it special is that it resolves all of the features discussed above. All of them!

### Push Inspector
Push Inspector is a react project written from scratch, removing all limitations of Task Group Inspector. From a technological point of view, Push Inspector uses React Redux to maintain a store and seperate the data from the presentation layer. It also keeps the UI sync with the URL using the famous routing library React-Router. It provides a progress bar as data visualization, showing the states of all tasks in an orderly fashion. Filtering the list of tasks can be done by clicking on one of the status colors in the progress bar. Last but not least, Push Inspector updates the list of tasks in real time!


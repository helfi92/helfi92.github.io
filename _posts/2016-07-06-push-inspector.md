---
layout: post
title: Task Group Inspector Rework
description: "Reworking Task Group Inspector from scratch"
modified: 2016-06-01T15:27:45-04:00
tags: [taskcluster, pushinspector, react]
image:
  feature: push-inspector/code-mac.jpg
  credit: 
  creditlink: 
---

Whenever a Mozilla employee or contributor pushes a build to a server, they go to treeherder interface to see and inspect the results of their build. The TaskCluster team decided to come up with a "developer-focused" treeherder interface and was later called Task Group Inspector. 

![alt text](/images/push-inspector/task-group-inspector.png)


The Inspector does a decent job inspecting a build, however it's still missing some important features. Let's discuss the latter.

###	Missing features

* When a build consists of tasks approaching a number close to a thousand and having to scroll the list of tasks as the sole option into finding that one failing task, it quickly starts to irritate the user. This is the case with Task Group Inspector.

* There is a web listener that listens to messages relating to tasks. A message will say what is the current status of a task. For example, a message can say the respective task went from a `running` to a `failed` state. Presently, the user interface does not update the changes in real time. It refreshes the page roughly every minute. This is definitely not the ideal case and can bring better satisfaction to the end user if things were done in realtime.

* The only way to currently know the status of the tasks is by scrolling down the list of jobs and inspecting manually. Data visualization is important to visualize large amount of data and identify new patterns, which the current UI is lacking.

* Task Group Inspector does not use Redux in its architecture. This is not preferred because it makes data attached to the views and brings complexity in handling the state of the application.

* The current interface handles routing with no powerful routing library which opens room for errors and complexity as the hash portion of the URL changes.

These missing features gave rise to Push Inspector, a tool that bring inspecting task groups to another level. What makes it special is that it resolves all of the features discussed above. All of them!

### Push Inspector
Push Inspector is a react project written from scratch, removing all limitations of Task Group Inspector. From a technological point of view, Push Inspector uses React Redux to maintain a store and seperate the data from the presentation layer. It also keeps the UI sync with the URL using the famous routing library React-Router. It provides a progress bar as data visualization, showing the states of all tasks in an orderly fashion. Filtering the list of tasks can be done by clicking on one of the status colors in the progress bar. Last but not least, Push Inspector updates the list of tasks in real time! Below are 2 videos showing you realtime updates and interaction with the dashboard.


<video class="center-image-video" width="560" height="315" controls>
  <source src="/images/videos/push-inspector/progressBar.mp4" type="video/mp4">
 	Your browser does not support the video tag.
</video> 
<b>
<video class="center-image-video" width="560" height="315" controls>
  <source src="/images/videos/push-inspector/authAndActions.mp4" type="video/mp4">
 	Your browser does not support the video tag.
</video>

### Where are we at with the new interface?
Most of the functionalities are implemented and I pushed the first pull request of Push Inspector. Once that is done, some time will be spent fixing the UI and probably add the option of receiving push notifications when a test fails or when a build is done. More updates to come soon. 

---
layout: post
title:  "Treeherder's Bottom Panel New Feature"
date:   2018-01-23 10:18:00
tags: [taskcluster, treeherder, react, actions]
---
A new feature was added to the bottom panel of a selected job. When the build system of the job is not buildbot,
there is now a hyperlink on the machine name that takes you to the worker explorer within Taskcluster to drill down
into a specific machine and see details like its most recent tasks. 

![Before optimization](/assets/images/registered-actions/th-bottom-panel.png)
*Treeherder's bottom panel*{: .img-caption }

In addition, you can perform actions on a machine. Actions can vary depending on the provisioner that provisioned the
worker. For example, all machines under `aws-provisioner` have an action to terminate the instance.

![Before optimization](/assets/images/registered-actions/worker-explorer.png)
*Worker explorer*{: .img-caption }

For more information on actions (e.g., defining new actions), refer to the
[action section](https://docs.taskcluster.net/reference/platform/taskcluster-queue/docs/actions#content.) in the docs.

## What do I do if there are problems?
* #taskcluster on IRC
* taskcluster::tools component in bugzilla
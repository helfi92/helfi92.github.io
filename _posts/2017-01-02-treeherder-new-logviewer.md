---
layout: post
title:  "Treeherder's New Logviewer"
date:   2017-01-02 10:18:00
tags: [taskcluster, treeherder, react, logviewer]
---
The current Treeherder logviewer is great, but It has a number of deficiencies. There is no way to scroll freely in the log without being blocked by the loading view, slowing down productivity. Not to forget, attempting to load a 100M+ log was out of the question. Taskcluster was having similar issues and so during Mozlondon we discussed about a possibility of having a shared project/tool that could solve both of our issues.

The Unified Logviewer is a tool created to replace Treeherder’s and Taskcluster's logviewer. It is a stand alone project written in React/Redux.

![Unified Logviewer](/assets/images/log-viewer/unified-logviewer.png)
*The Unified Logviewer embedded inside Treeherder*{: .img-caption }

## Features
1. Handles 100M+ log files. By downloading the file as an array buffer, we can get a fast download without doing a parse of all the data, which is what crashes the browser normally. We use the scrollbar position to identify which portion of the array buffer to parse and display on the screen.
2. Renders ANSI colors.
3. Viewport and resizing. The Unified Logviewer is embedded through an iframe, so it’s really easy to size/resize.
4. Can pass data either via query string or using `window.postMessage` to the iframe for the query parameters you wish to change.
5. Linkability: Since everything is hooked to the query string, we can link to a log and any state provided through there.
6. Highlighting: You can highlight individual and multiple lines.

**Note**: We created an API for the logviewer to pass in custom CSS in order to match the logviewer theme of Treeherder. The Unified Logviewer is one that is shared across any site and is not exclusive to Treeherder. It drew inspiration from the Travis logviewer for styling. For a quick demo of the viewer, without overriding the CSS:

[https://taskcluster.github.io/unified-logviewer/?url=https://public-artifacts.taskcluster.net/5qB6Zqc0Tw2_wkKwHgGNGQ/0/public/logs/live_backing.log](https://taskcluster.github.io/unified-logviewer/?url=https://public-artifacts.taskcluster.net/5qB6Zqc0Tw2_wkKwHgGNGQ/0/public/logs/live_backing.log)

## Needs work
1. Full-text searching and filtering. There is a current implementation of a KMP string search algorithm [1] already in place in the component, but it’s missing a UI component within Treeherder as well as exposing an API for it.

## Contribute
* Issue Tracker: [https://github.com/taskcluster/unified-logviewer/issues](https://github.com/taskcluster/unified-logviewer/issues)
* Source Code: [https://github.com/taskcluster/unified-logviewer](https://github.com/taskcluster/unified-logviewer)

[1] [https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm)

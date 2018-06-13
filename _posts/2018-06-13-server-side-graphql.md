---
layout: post
title: Server Side GraphQL
date:   2018-05-18 10:18:00
tags: [taskcluster, redeployability, graphql, server]
---
The Taskcluster team is experimenting with GraphQL.
For the "What" and "Why", I invite you to read my
[initial blog](https://hassanali.me/2018/05/18/rethinking-our-ui.html)
where I write about the motivation and benefits to using GraphQL.
This short post will discuss the server component of GraphQL and how it's being used.

The server side component of GraphQL can be implemented in one of two ways:
* Natively from the APIs
* Through a GraphQL layer for the web app

## Natively from the API

A native support will add a burden to the team to maintain two APIs (REST and GraphQL)
with little performance change. This is because our biggest bottleneck in the backend
is the [queue](https://github.com/taskcluster/taskcluster-queue) which uses Azure
(slow read performance) as its primary data store.
[RFC#65](https://github.com/taskcluster/taskcluster-rfcs/pull/65) was created to migrate
the database to Postgres which should bring significant performance improvements to
the server. At this time, there are other tasks like the migration that are of a
higher priority than the native support for GraphQL. We will revisit this approach later.

## A GraphQL layer for the web app

This would involve adding a gateway between the web app and Taskcluster clients.
This layer would read GraphQL queries sent from the client and then use `taskcluster-client`
REST API to proxy requests back to Taskcluster microservices.

Thanks to [Eli](https://twitter.com/@eliperelman)'s work, he authored `taskcluster-web-server`,
a GraphQL web server, to support the taskcluster-web UI repository. It exposes a single endpoint
(the graph) capable of fetching data from any of the clients endpoints via `taskcluster-client`.

## Closing Remarks

Introducing a GraphQL layer would not be maintained like our other Taskcluster services;
it would be part of a server instance specifically designed to support the web app.
The eventual goal would be to throw this proxy away and access all APIs via GraphQL.
This can be done after we have more experience  with the query language, investigate
the unknowns, and successfully migrated the
[queue](https://github.com/taskcluster/taskcluster-queue) to Postgres.

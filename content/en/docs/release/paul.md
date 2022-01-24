---
title: "New Database Connector Authentication Features are live"
description: "My Fivetran Simulation"
lead: "January 24, 2022"
date: 2020-11-28T08:48:57+00:00
lastmod: 2020-11-28T08:48:57+00:00
draft: false
images: []
menu:
  docs:
    parent: "Fivetran simulation"
weight: 100
toc: true
---

## What's new?

We’ve just enabled a series of Connector authentication features for selected customers in private beta.  

## How these changes benefit users

Users without admin privileges have reported authentication friction when attempting to troubleshoot their Database Connectors. Where API connectors merely require API keys and secrets, Database Connectors require credentials that some users can’t easily access.

These new authentication features run inside the customer’s existing VPC.  We expect these improvements to address the authentication blockers users have reported and streamline the troubleshooting experience with a series of measures that take place under the hood:

- Connectors now check for allowlisted IP addresses.
- Connectors now validate whether a user’s login is enabled.
- Connectors now check whether the database is in maintenance status.
- Fivetran now alerts customers automatically when issues arise.

## Required Actions

Customers in private beta already have access to the new features. If you’d like to learn more, check out our internal design document on Database Connectivity Improvements.  To view the files we’ve changed to enable these features, locate the `connectivitychecker.java` file within the Connectivity checker module.

## Feedback

Feel free to post any questions or feedback you may have in our #database-connectors project channel, or reach out to @IanWoods. 

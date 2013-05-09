---
layout: post
title: Functional testing in Symfony with a different user provider
date: 7th December 2012
excerpt: How to use a different user provider (and authentication method) in your functional tests, than is used in your main app
---

The main security configuration for the application is form_login, with a custom authentication
 provider and user provider (these parts are farmed out to an API).

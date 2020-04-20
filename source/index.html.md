---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - curl
  - java
  - javascript

toc_footers:
  - <a href='https://notihub.io/sign-up'>Sign Up</a>
  - <a href='https://notihub.io'>2020 © Notihub</a>

includes:
  - authentication
  - customers
  - customers_contacts
  - customers_preferences
  - messages
  - errors

search: true
---

# Introduction

Notihub allows you to sends custom templates easily updated by our Web UI, through thousand of different customers in Web or Mobile devices!

We allow you to configure a different set of providers like Twilio, Sendgrid, AWS, Mailgun, and much more to send different email, sms or even push notifications.

# Getting Started

* Create an account to obtain your environment and api keys 
* Download any of our API SDK to fast access
    * <a href="https://github.com/notihub-io/notihub-java-sdk" target="_blank">Java SDK</a>
    * <a href="https://github.com/notihub-io/notihub-nodejs-sdk" target="_blank">Node.js SDK</a>
* Use those keys to start sending messages! 

#Environments

Notihub supports the creation up-to 20 environments per account. When you register your account, we will create for you the `development` and `production` environments. You can also create new ones like `staging`, `testing`, `dev-green`, `dev-red` or whatever other you need to adapt to your development flow. 

Keep in mind, that each customer you create, or message you sent will only be part of one environment. But all your environments will run through your quota usages. 

Each environment will have a pair of `clientId` and `clientSecret` keys, that you can use with your `publicKey` from you account, to obtain an access token.


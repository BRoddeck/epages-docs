---
layout: page
key: apps-develop-app
title: Developing an app
---

# Registration

Before you can start coding off you'll need to sign up for the ePages Developer Program. Gain access to all the resources you need to create your app successfully.

{% image https://www.filepicker.io/api/file/CuP4suO0RH6H4BkKT9GI %}
This is how the registration screen looks like.
{% endimage %}

## Sign up for free.

1. Enter your name and email address in the required fields.
2. Agree to the terms and conditions.
3. Check your mailbox for the login details from your registration and create a test shop.

{% callout info Helpful stuff! %}
  If you're eager for more information on how to set up your demo shop, check out our [Online Help](https://www.online-help-center.com/) for merchants.
{% endcallout %}

# OAuth

Your app cannot access the REST API resources without authenticating first. In order to access to the ePages data, your application must be authenticated.
All API calls are authenticated according to the OAuth 2.0 protocol. Fancy more detailed information? Here you go: [OAuth 2.0 protocol](https://tools.ietf.org/html/rfc6749).

Once you have signed up for the developer program and you have created your app successfully, you will be assigned a unique **Consumer key** and **Consumer secret**. Remember these credentials as you will have to integrate them into the configuration files or the actual code of your application.

{% callout danger Important! %}
For your application's own security: DO NOT share your consumer secret with anyone!
{% endcallout %}

The credentials you'll receive might look like this:

{% image example-consumer-key-secret.png 50% %}
Example of OAuth keys
{% endimage %}

## Authorisation

1. Within your test shop backoffice, again choose the tab **Apps**.
2. Choose the field **Private Apps** and click the button **Create app**.
3. Click the button **Test authorisation**. You will be requested to enter the **Application callback URL** and **Application notification URL**.
4. Click the button **Test authorisation** again. You will be forwarded to an external page to complete the authorisation process.
5. Once the test authorisation has been finished successfully, you will be fowarded to

{% callout danger Authorisation to be described %}
  development in progress
{% endcallout %}

# Create an app

## What kind of apps do we support?
In a first approach, we only support external apps. We call them "connectors". Connectors are not integrated into the merchant's backoffice, but interact with a shop via the REST API to use the shop's data.

## Set-up
1. Within your test shop backoffice, choose the tab **Apps**.
2. Choose the field **Private Apps** and click the button **Create app**.
3. Fill out the entry field **App name**. This field is mandatory.
4. Click the button **Save**. The API data **Consumer key**, **Consumer secret**, **Access token** and **Access secret** will be generated automatically.

Once this is done, you can start coding off. Brilliant: you are free to use any kind of code language.
But when developing your app, please consider our [API call limit](page:apps-using-the-api#api-call-limit).

## Your app is ready for takeoff?

Let's go for the [authorisation](page:apps-develop-app#authorisation).
Once this is done, your app will be available in your test shop in the field **My apps**.

## Test. Test. Test.

| What?             | What for?                                                                              |
|-------------------|----------------------------------------------------------------------------------------|
| Delete an app.    | Removes the keys of the app in the **Private app** environment. Testing not mandatory. |
| Uninstall an app. | Ensures, that your app is capable of being uninstalled completely. Testing mandatory.  |

## Delete an app:

In the field **Private apps** click the button **Delete**.
If the process has been successful, the app has been removed from **Private apps**.

## Uninstall an app:

1. In the field **My apps**, click the app.
2. In the new window, click the button **Uninstall**.
3. A dialogue appears that you're about to uninstall your app.
4. Click the button **Uninstall**.

If the process worked just fine, the field **My apps** is empty.
Your app is still available in the field **Private apps**.

## Ready, steady, go!

[Submit your app](page:apps-develop-app#submit-an-app).

# Submit an app

Yay! You're done with developing your awesome app and you're excited to make it available to our App store? Testing is done and everything works fine? Congrats! We are just as much excited as you are!

Let's go submitting your app.

1. Within your test shop, click the tab **Private apps**.
2. Click the button **Submit app**.
3. Fill out the submission form.

This is what we need to thoroughly test the functionality of your app.

| What?              | What for?                                                                                                                                          |
|--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Consumer key       | Identifier string generated by API for use in OAuth 1.0.                                                                                           |
| Consumer secret    | Unique token generated for use in OAuth 1.0 as an element of creating a signature. The consumer secret should not be shared.                       |
| OAuth token        | Token generated by the server upon signed request. Can be either `request_token` or `access_token` depending on which step in the flow you are on. |
| OAuth token secret |                                                                                                                                                    |

# You have reached the home stretch!

Click the button **Submit app now**.

Makes us jump for joy! Your app is on its way! We will be testing your app and come back to you as soon as our review has been completed. Give us XXX days time.

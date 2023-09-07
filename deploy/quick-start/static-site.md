---
title: "Static Site"
date: 2023-09-06
description: "Deploy Static Website on Candle Cloud"
weight: 1
---

Candle Cloud allows for free website hosting and allows you to bring your own domain for ssl certificates. Deploying your site to Candle Cloud is painless and simple.

## Prerequisites

- Create a free account on [Candle Registry](https://registry.candle.dev)
- Create a namespace for your apps in Candle Registry
- [Install Wick CLI](https://candle.dev/docs)
- Configure Wick with your [registry credentials](https://candle.dev/docs/candle-cloud/registry/#3-getting-your-wick-cli-registry-credentials)

## Create an Application Manifest

- On your computer, navigate to the parent directory containing your static html directory.
- Create a file named app.wick.

```bash
.
├── app.wick
└── www
    └── index.html
```

Paste this into your app.wick, change the namespace appropriately as you created in step 2 of the Prerequisites.

```yaml
---
kind: wick/app@v1
name: my-site
metadata:
  description: This is my static site
  version: 0.1.0
package:
  registry:
    host: registry.candle.dev
    namespace: *MYNAMESPACE*
  files:
    - '{{__dirname}}/www'
resources:
  - name: httpserver
    resource:
      kind: wick/resource/tcpport@v1
      port: "{{ ctx.env.HTTP_PORT }}"
      address: 0.0.0.0
  - name: DIR
    resource:
      kind: wick/resource/volume@v1
      path: '{{__dirname}}/www'
triggers:
  - kind: wick/trigger/http@v1
    resource: httpserver
    routers:
      - kind: wick/router/static@v1
        path: /
        volume: DIR
        fallback: index.html
```

## Running Locally

In your terminal, run: `HTTP_PORT=8080 wick run app.wick`
This will start the application on port 8080. You can load up your browser to see your site locally.
![Run Wick Locally](/run.png)

## Push to Registry

In your terminal, type: `wick registry push app.wick`
This will push the project to your namespace on [registry.candle.dev](https://registry.candle.dev)

Make sure to note the reference, it will be similar to the following:
`registry.candle.dev/mynamespace/my-site:0.1.0`

![Wick Registry Push output](/push.png)

## Deploy on Candle Cloud

- Navigate to the Candle Cloud dashboard.
- Click on the ‘+’ icon next to Deployments.
- Add a name and Registry reference for your application.
- Click on ‘Deploy Application’ to start the application.
- After a few minutes, your site will be up on a dynamically created hostname.
- [Upgrade your entitlements for free.](https://cloud.candle.dev/entitlements)

![Deploy application screen on Candle Cloud](/deploy.png)

## Adding Your Own Domain Name (Optional)

If you have your own custom hostname that you want to use, you can add the domain by clicking the `+` icon next to Domains and entering your domain and associating it with your deployment. You will need to update the IP address provided in the dashboard in your DNS provider.

## Extend your application

Check out the [example apps repository](https://github.com/candlecorp/wick-apps) to get more idea like adding authentication and database backed rest-api functionality to your applications. Candle Cloud is not just a static site host, you can build powerful applications with Wick and host them for free! Join us on our [Discord server](https://discord.gg/candle) and we will be happy to help you with your next project on Wick!

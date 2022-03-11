---
template: guide
title: PM2
description: How to deploy Nuxt with PM2 cluster mode enabled?
target: Server
category: deployment
logo:
  light: "/img/companies/square/light/pm2.png"
  dark: "/img/companies/square/dark/pm2.png"
---
# Deploy Nuxt using PM2

How to deploy Nuxt with PM2 cluster mode enabled?

---

Deploying using [PM2](https://pm2.keymetrics.io/) (Process Manager 2) is a fast and easy solution for hosting your universal Nuxt application on your server or VM.

In this guide, we build the application locally and serve it though a PM2 config file with the cluster mode activated. Cluster mode will prevent downtime by allowing applications to be scaled across multiple CPUs.

## Getting Started

1- Make sure you have pm2 installed on your server. If not, simply globally install it from yarn or npm.

```bash
# yarn pm2 install
$ sudo yarn global add pm2 --prefix /usr/local

# npm pm2 install
$ npm install pm2 -g
```
2- Make sure to install `nuxt-start` [package](https://www.npmjs.com/package/nuxt-start) , the bunchline of this package is to : 

>Start Nuxt Application in production mode.


```bash
$ npm install --save nuxt-start
```
3- edit your `package.json` to do the following :
replace this : 

```json
    "scripts": {
		"start": "nuxt start"
     }
```

with the following : 

```json
        "scripts": {
		"start": "nuxt-start" ### note the dash
	}
```



## Configure your application

All you need to add to your universal Nuxt app for serving it though PM2 is a file called `ecosystem.config.js`. Create a new file with that name in your root project directory and add the following content:

```javascript
module.exports = {
  apps: [{
    name: 'NuxtAppName',
    exec_mode: 'fork',
    instances: 'max',
    autorestart: true,
    script: './node_modules/nuxt-start/bin/nuxt-start.js ',
    args: 'start',
    env_production: {
      PORT: 3000,
      NODE_ENV: "production"
    }
  }]
}
```

## Build and serve the app

Now build your app with `npm run build`.

And serve it with `pm2 start ecosystem.config.js --env production`.

Check the status `pm2 ls`.
you will see something similar to the following : 

```bash
┌─────┬──────────────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
│ id  │ name             │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
├─────┼──────────────────┼─────────────┼─────────┼─────────┼──────────┼────────┼──────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 1   │ NuxtAppName      │ default     │ N/A     │ fork    │ 518703   │ 97s    │ 1    │ online    │ 0%       │ 74.6mb   │ tawfekov │ enabled  │
└─────┴──────────────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘
```

Your Nuxt application is now serving in `production` mode!

## Further Information

This solution guarantees no downtime for your application on this server. (You should also prevent server failure through redundancy or high availability cloud solutions.)

You can find PM2 additional configurations [here](https://pm2.keymetrics.io/docs/usage/application-declaration/#general).

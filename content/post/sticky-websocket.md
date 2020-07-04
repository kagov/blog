---
title: Dynamic load balancing WebSockets using SocketIO and Traefik edge router 
date: 2019-08-12 11:41:07
tags:
draft: true
---
Maintaning a web socket connection in replicated services on the cloud<!--more--> does not work out of the box. As long as there is only on server holding all the web socket connections things work just fine but once we move try to scale the service by creating replicas then even a simple websocket application seems to fall apart. Let us look at the problem in a little more detail.

When you connect to a server using socket io client




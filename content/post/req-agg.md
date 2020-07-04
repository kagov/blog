---
title: Aggregating response using OpenResty
date: 2019-07-19 18:08:53
tags:
---

In today's world of lean Microservices each service performs a specific task and and often at times the client has to make multiple API calls to <!--more-->the server. This increases the chattiness between the two entities and the client ends up making multiple API calls just to render a single page. You can read more about this [here](https://netflixtechblog.com/optimizing-the-netflix-api-5c9ac715cf19). Netflix has solved this at a larger scale and their approach touches multiple layers of the stack, such as languages, frameworks, architecture etc.

In this post I wish to demonstrate how you can aggregate responses from multiple microservices and respond to the client in a single combined response.

OpenResty is a web platform that sits on top of the mighty Nginx and allows you to write dynamic scripts that can perform various functions and one of those is response aggregation.

Note: The implementation involves touching upon various technologies like NodeJS, Docker etc. but we would like to focus more on the lua part.

## Lets begin

### Step 1. Preparing two NodeJS services

We are going to run two separate NodeJS services containing an API each and OpenResty will aggregate responses from these services and deliver a single response. The sole reason for choosing NodeJS and express is for the sake of simplicity but at the end of the day openresty is a proxy and it will work with any app server.

``` javascript
app.get('/service-one/json', function(req, res, next) {
    var obj = {
    "status" : "response form service two"
  }
  res.json(obj);
});
```
The above code snippet is  a simple GET API that returns a json response. The real use case would be different but one can get the gist. The second service would also have a similar API but the endpoint might read **/service-one/json**.

Serve the below services in the port of your choice.

``` javascript
app.listen(3001, function () {
  console.log('listening on port 3001!')
});
```
Run both the applications

``` shell
node app.js
```

### Step 2. Setting up OpenResty components

Now that we have our sample services out of the way let us now look at the main component of this exercise. Before starting the OpenResty server we need to specify the configuration and the lua script. Create a **conf.d** folder and then create a file for the endpoint configuration (**default.conf**).

``` txt
server {
listen 80;
location /agg {

content_by_lua_file /etc/nginx/conf.d/lua/agg.lua;

}

location /service-one {
proxy_pass http://192.168.1.12:3001/service-one/json;
}

location /service-two {
proxy_pass http://192.168.1.12:3002/service-two/json;
}

}
```
Once again this is a very rudimentary configuration file that is just enough to illustrate the response aggregation concept. So the server listens at the 80 port and there are 3 location directives. The **service-one** and **service-two** will just access the nodejs servers separately and get the individual response. Please note that the IP address will be different in your case and and make sure you use the IP address of your machine and **not localhost**. So we are more interested in the **/agg** endpoint which actually invokes the lua script that will perform the aggregation. 

So one of the main powers of OpenResty is that it adds lua scripting into the vanilla Nginx. According to their [Github](https://github.com/openresty/lua-nginx-module) 
> If you are using this module, then you are essentially using OpenResty.

And naturally, **ngx_http_lua_module** is not distributed with Nginx source. 

**content_by_lua_file Directive** is a custom directive that executes the lua script present in the file path (in this case it is present inside the **/etc/nginx/conf.d/lua** directory). Feel free to have a deeper look on how this works [here](https://github.com/openresty/lua-nginx-module#content_by_lua_file)

Now remember the **conf.d** directory we created earlier, go ahead and create another directory called **lua** and then create a **agg.lua** file. Add the below script to the file.

``` lua
-- the json parser for lua
cjson = require("cjson")
-- the main portion that calls both the services and stores the response in
-- two variables
local res1,res2 = ngx.location.capture_multi{{"/service-one"},{"/service-two"}}
-- Setting the nginx variables related to the response
-- status code
ngx.status = ngx.HTTP_OK
--  content type header
ngx.header.content_type = "application/json; charset=utf-8"
-- now retrieve the body of the response using the cjson parser
local status1 = cjson.decode(res1.body)
local status2 = cjson.decode(res2.body)
-- finally construct the aggregated response
ngx.say(cjson.encode({service1=status1.status,service2=status2.status}))
return ngx.exit(ngx.HTTP_OK)
```
I have added inline comments to explain each line of the script. For larger applications one might have to write multiple such scripts and aggregate more the two responses.

So at the end of step two we have two applications running on NodeJS and a **conf.d** directory that looks like this.

``` txt
conf.d/
├── default.conf
└── lua
    └── agg.lua 
```

### Step 3. Starting OpenResty
For this post I am going to use Docker to spin up an OpenResty container but you can run it on your machine bare bones. Check out their installation guide [here](https://openresty.org/en/installation.html). Below is a very simple Dockerfile for running OpenResty that downloads the latest image from their repo and then copies the **conf.d** directory into the **/etc/nginx/conf.d/** directory. By default nginx will look for all the configuration inside this directory.

``` dockerfile
FROM openresty/openresty:latest
COPY conf.d/ /etc/nginx/conf.d/
```
Build the image and run the container

``` sh
my-pc$ sudo docker build -t oresty:aggregation .
my-pc$ sudo docker run -it -d -p 8080:80 oresty:aggregation
```

If the above two steps are successful then you should see a container running in the background (-d flag) whose 80 port is listening to the host port 8080 (-p host_port:container_port).

Head out to your browser and hit **localhost:8080/agg** and if you see the below response then we have successfully performed response aggregation.

``` json
{
    "service2": "response from service two",
    "service1": "response from service one"
}
```

You can also access the services indivisually using the **service-one** or **service-two** endpoints but you know that already :). Check out all the remaining bits and pieces in the [github](https://github.com/kagov/oresty-aggregation) repo.
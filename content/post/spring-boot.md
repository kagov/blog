---
title: Boot up your Spring Boot app
comments: This is a sample excerpt
tags:
  - spring
  - spring boot
date: 2017-04-26 21:36:06
---
Get started with Spring Boot in a matter of minutes.
<!--more-->

Get your server up and running in a matter of minutes. You will be amazed with the little amount of code you will have to write

## Lets begin

Create a simple maven project in Eclipse or any other supported IDE.

Based on the default settiongs of your IDE you will be able to find a class named App.java inside your default package. If not, help yourself and create one.

#### Remove the boilerplate code and paste the following lines shown below.

``` java
@SpringBootApplication
@RestController
public class App {

    public static void main( String[] args)   {
        SpringApplication.run(App.class, args);
    }

    @RequestMapping(value = "/", method = RequestMethod.GET)
        public @ResponseBody Map<String,Object> hello()  {
        Map<String,Object> response = new HashMap<String, Object>();
        response.put("status", "succcess");
        response.put("message", "Welcome to Spring Boot");
        return response;
    }
}
```
What do you see? A lot of errors of course.

#### That is because we forgot to include the key ingredient.

Open pom.xml and include Spring Boot as a dependency. You can find the maven dependency [here](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web)

Your IDE will download all the dependencies, if it doesn't then rght click on your project--Maven--Update Project. You can also press alt+F5.

That is it. That is all the code you have to write. Now run this as a Plain old Java application. The default server port is 8080 hence you can fire up your favourite browser and hit localhost:8080 and voila!!

### It's your first API

Spring will boot up in a matter of seconds

**Note: This is not the traditional way of doing this but this post is just meant for you to get started.**

We have to create separate classes for the controllers so that all the services are at one place. A detailed post will also cover the basic project structure and will explain about the typical files that you will find in any Spring project.

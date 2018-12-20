---
layout: post
title: "Burp Infiltrator, Spring Boot and Docker"
date: 2018-12-20
---

The other day, I set up Burp Infiltrator. During its setup, I ran into an issue or two, hence this blog post. So you don't have to run into the same ones. But before we dive in, let's have a look at what Burp Infiltrator actually is.

The [Burp Infiltrator](https://portswigger.net/burp/documentation/infiltrator) is part of Burp Suite. It allows instruction of the applications, so that that during a pentest, you can detect if the input landed in potentially unsafe API calls. Massively simplified - that's it. And it’s awesome!

None of what is written in this post is mind-boggling, but since the community using the Infiltrator seems way smaller than the one using the rest of Burp, I decided to put it up anyway.

## Spring Boot
[Spring Boot](https://spring.io/projects/spring-boot) is a Java framework that I get to see a lot lately. Applications with Spring Boot can be configured to be completely stand-alone. Those apps have a bash script prepended to the actual jar, inside the jar file. So the actual app file is a bash script _and_ a jar file. And there are some more specialties attached to it. More about the deployment can be found in the [official documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/deployment-install.html). 

```bash
$ head springboot-app.jar
#!/bin/bash
#
#    .   ____          _            __ _ _
#   /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
#  ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
#   \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
#    '  |____| .__|_| |_|_| |_\__, | / / / /
#   =========|_|==============|___/=/_/_/_/
#   :: Spring Boot Startup Script ::
#
```

If you just apply the Infiltrator on the jar, it will kill the script part. So save first, instruct afterwards. 

{% gist e9aa1b17a9ebe62a4629cceff93d4c88 %}

Some background info on this:
- It seems like `exit 0` is a good string to search for right now. Might have to be adapted, if Spring Boot changes the start script
- Of course the Burp Infiltrator must be adapted to your own needs
- A current limitation of Burp Infiltrator: One can’t directly infiltrate a jar, It must be located inside a folder
- If you try to jar the file with compression, it will complain about double compressed jars at startup
- If you try to jar the file without compression, it will complain about missing `MANIFEST` at startup
- It works fine with no compression and zip
- My umask by default doesn’t include execution rights, thus the `chmod`

## Docker
The official Infiltrator documentation states:

> If the bytecode is already located on the target application server, ensure that the application is not currently running, as this may prevent the bytecode on disk from being modified.

Most applications I pentest run inside docker. Also most of the time, the application is part of the docker image itself and not on a mounted volume. Since the docker is launched directly from the build pipeline. I have two options, to either instruct the jar during build time as e.g. gradle task, or directly on the host of the running containerized app. For my case, it currently simpler to do the latter. But this could change with more usage of Burp Enterprise. What seems to work quite well is the following:

```bash
docker container ps # find your relevant process
docker container cp container-name:/path/to/app.jar dst/on/localhost 
# infiltrate the jar
docker container stop container-name
docker container cp infiltrated-app.jar container-name:/path/to/app.jar
docker container restart container-name
```

Note:
Older versions of docker will need:

```bash
docker ps
docker cp
docker start
docker restart
```

While this is all of course not particularly new or generic, I hope it helps for those that are trying to combine Burp Infiltrator with dockerized apps and/or Spring Boot apps. Please let me know if you found better ways to deal with either of those. 

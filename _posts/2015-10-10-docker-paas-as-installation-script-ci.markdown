---
published: true
comments: true
layout: post
title: How to use docker as an CI toolkit
category: howto
tags: [docker, ci, experiment]
date: 2015-10-22 20:51:23 +0800
---

## docker be used as CI? You serious?
Since hub.docker.com offers many barebone distributions and containers are so fast to build and remove. That made the sense!   
The better part of docker is that there would be tons of PaaS and image building is isolated from PaaS serving containers.

# What's your solution?
### 1. Build image with RUN /path/to/script.sh on the specified distribution.
If any script exit with non-zero, docker daemon would catch it and abort the build process.    
We can assert script is able to __run__ under specified distribution.

%%MORE%%

### 2. Run fresh built images in container
{% highlight bash %}
$ docker run -d <container name/ID>
$ docker logs <container name/ID>
# If you want to debug further within the container:
$ docker commit <container ID> tmp_image
$ docker run --rm -ti --entrypoint=/bin/bash tmp_image
{% endhighlight %}
If the log turns out to be sane, then it's a [duck][wikipedia duck type].    
If no luck in logs, go bumming your scripts, you get an __BAD__ from docker CI.


## Reference
[CI experiment on hyper-installer BASH script][github imdjh/hyper-installer]    
[Run command in stopped container][docker forum link]


[github imdjh/hyper-installer]: https://github.com/imdjh/hyper-installer
[docker forum link]: https://forums.docker.com/t/run-command-in-stopped-container/343/7
[wikipedia duck type]: https://en.wikipedia.org/wiki/Duck_typing

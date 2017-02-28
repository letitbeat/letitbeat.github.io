---
title: How To Clean Up Used Space by Docker
date: 2017-02-25 22:37:18
tags:
  - docker
  - how to
---

Using Docker has become very handy to must of us when working with multiple environments. However, one problem that we noticed is the incredible disk usage of it. So in this post I will list useful commands that could help us solving the issue.
<!-- more -->
   
First, you must check and remove stopped Docker containers that will no longer be used. 

To list this you can run:

{% codeblock lang:bash %}
   docker ps -a
{% endcodeblock %}

After selecting the unused containers, you can remove them with the following command:

{% codeblock lang:bash %}
   docker rm -v {name or hexadecimal container identifier}
{% endcodeblock %}

{% alert info %}
  The flag -v is used to also delete any associated volume.
{% endalert %}

Then, following command should be executed

* To delete "dead" containers. (containers with status=dead whose creation failed):

{% codeblock lang:bash %}
   docker ps -f status=dead --format '{% raw %}{{ .ID }}{% endraw %}' | xargs -r docker rm -v
{% endcodeblock %}

* To delete volumes not associated with any containers ( This usually happens when container are deleted without -v flag of docker rm command):

{% codeblock lang:bash %}
docker volume ls -qf dangling=true | xargs -r docker volume rm
{% endcodeblock %}

* To delete images not longer referenced by any tag (<none>):

{% codeblock lang:bash %}
docker images --digests --format '{% raw %}{{.Repository}}:{{.Tag}}@{{.Digest}}{% endraw %}' | sed -ne 's/:<none>@/@/p' | xargs -r docker rmi
{% endcodeblock %}

* To delete images not longer referenced by any name:

{% codeblock lang:bash %}
docker images -qf dangling=true | xargs -r docker rmi
{% endcodeblock %}

* To delete temporary files (blobs which were left by some interrupted docker pull):

{% codeblock lang:bash %}
rm -f /var/lib/docker/tmp/*
{% endcodeblock %}

{% alert warning %}
Note: This files are useful in case we want to resume the docker pull command.
{% endalert %}

Hope you find this post useful and keep Dockering! 

---
date: 2022-12-08T18:38:21Z
description: ""
draft: false
slug: "using-docker-to-compress-and-extract-volume-backup"
title: "Using docker to compress and extract volume backup"
---


## The problem

Sunday afternoon I spend playing with docker backups and exporting them locally. My problem was that I had remote machine with running containers in Docker. Containers had docker volumes which weren't mapped directly to local filesystem, simply:

```
...
volumes:
  - data_config:/src/models/data
```

Because it's not mapped on local filesystem this gives freedom to operate with data inside these volumes.

Now we need those data inside container `/src/models/data` and since we are lazy we don't want to log to machine copy them to machine filesystem and then copy them to local machine.

## Create docker context

First we can create local docker context:

```
docker context create machine01 --docker "host=tcp://192.168.28.100:2375"
```

This can help us to connect docker containers and volumes while staying on local machine. Bear in mind to use TCP connection with port 2375 you need to set docker API first. How setup this take a look [here](https://www.howtogeek.com/devops/how-to-get-started-using-the-docker-engine-api/).

## How to copy files to local machine

With the help of context we can list volumes like (as well as access to containers*):

```
docker volume ls
```

Now we can access to containers, volumes on the remote host via docker api. All we need now is to smuggle data from volume to our local machine. Here is the script I created to solve this problem:

{{< gist axeII ed27647ce7e5778f1255340d260c555b >}}

Is does that lists volumes. Using tool [fzf](https://github.com/junegunn/fzf) (a great tool I strongly recommend to use) have the ability to scroll these listed volume. After you choose volume it's mounted inside `busybox` container. Script is going to compress whole directory and at last download it via STDOUT to your local directory.

## Which compression algorithm is best

When I worked on the last part of the script, I had two problems. First I need to do it effectively since we are moving whole directory. We can pack whole directory into single file using `tar`. The second problem is that we need to choose compression algorithm.

Using the gzip format file size was `420MB` instead of `500MB`:

And the time was:

```
time bash backup-docker-volume.sh
tar: removing leading '/' from member names

________________________________________________________
Executed in   57.13 secs    fish           external
   usr time    0.74 secs    0.11 millis    0.74 secs
   sys time    4.62 secs    1.39 millis    4.62 secs


```

Using the `zstd` compression, the file size was `470MB` instead of `500MB`:

And the time was:

```
tar: Removing leading `/' from member names

________________________________________________________
Executed in   61.08 secs    fish           external
   usr time    0.43 secs    0.13 millis    0.43 secs
   sys time    2.64 secs    1.88 millis    2.64 secs

```

Final try was the xz compress algorithm. Although I was unable to use it on either of those images (busybox and custom image). There might have the biggest compression from those three, but at the time of writing I could not find any minimal docker image containing this compression algorithm.

## Final thoughs

I was disappointed with `zstd`. First in my test it was slower than `gzip` and bigger. Meaning there is not upper side to choose this compress algorithm. Plus `gzip` can be used with the `busybox` image. No need any special unstrusty image (though you could build one your self and pushit to registry). Very light simple image for this use case.

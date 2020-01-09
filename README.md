# What's this?

This is a _docker_ image for the reverse engineering of _Android_ applications.

**Disclaimer**: Please use responsibly.

# Quick start

Don't want to read this page through and have only basic requirements?

```bash
$ docker pull cryptax/android-re
$ docker run -d --privileged -p 5900:5900 -p 5022:22 --name androidre cryptax/android-re
$ xhost +
$ ssh -p 5022 -X root@127.0.0.1
```

Login with password `rootpass`.

Then:

```bash
$ emulator9 &
```

If that's not working the way you expect, read the rest ;P


# Description

This container contains many tools to reverse engineer Android applications.

- Android emulators 5.1 (ARM), 7.1.1 (ARM) and 9.0 (x86)
- androguard
- apktool
- AXMLPrinter
- baksmali / smali
- classyshark
- CFR
- dex2jar
- enjarify
- frida
- google play api
- google play crawler
- google play downloader
- jadx
- java decompiler
- krakatau
- procyon
- radare2

Those are open source tools, or free demos.

[![](https://images.microbadger.com/badges/image/cryptax/android-re.svg)](https://microbadger.com/images/cryptax/android-re "Get your own image badge on microbadger.com")

# How to use this image

There are three steps:

0. Install docker ;)
1. Download docker image
2. Run the container on your host
3. Log into the container and use it ;)

## Retrieve the image

Normally, you just need to do:

```
$ docker pull cryptax/android-re:latest
```

Unless you want to build your own image - then see below the _Customization_ section.

## Running the container

There are a few options:

- running the container locally: you just want to run on your own machine and don't want to bother about SSH or VNC.
- running to connect via SSH or VNC: the container will be available as a standalone host you can log into via SSH, or VNC.

### Running the container locally

```
$ docker run -it --rm -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix cryptax/android-re:latest /bin/bash
```

You are directly connected to the container.

Note you can also share a directory with your host using `-v hostdir:containerdir`.

If you try to use any graphical interface and get an error like `No protocol specified` followed by an crash (`SEGFAULT`), using this command before running the docker should fix it: `xhost +local:docker`.

**IMPORTANT**: if you want to use the Android emulator x86 image, you need to set the `--privileged` option in the command line, i.e:

```
$ docker run -it --privileged --rm -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix cryptax/android-re:latest /bin/bash
```

### Running to connect  via SSH or VNC

Run the container:
```
$ docker run -d --name androidre -p SSH_PORT:22 -p VNC_PORT:5900 cryptax/android-re
```

where:
- NAME is the name you wish to give to this container. You may start several different instances.
- SSH_PORT is the port number the container will listen on for SSH connections. As the standard SSH port (22) is often already used, you may want to use another port here.
- VNC_PORT is the port number the container will listen on for VNC connections.

**Note**: if you have `docker-compose`, you can use (or tune) `docker-compose up -d` to launch the container.

For other options in `docker run`, [please go to docker's documentation](https://docs.docker.com/engine/reference/run/). For example, you may want to mount a given directory of your network and make it accessible to the container using `-v` option.

Typically, I run (but you may have to modify to suit your own needs):
```
$ docker run -d --name androidre -p 5022:22 -p 5900:5900 cryptax/android-re
```

Do not forget to add `--privileged` if you are using the x86 emulator.


## Connecting via SSH or VNC

Once a container is running, it's basically like a virtual Linux host. You need to connect to it.
You are expected to log in using either **ssh** or **vncviewer**.
The default root password is **rootpass**.

### Logging in with SSH

Use `-X` to forward X window. 

```
$ ssh -X -p SSH_PORT root@127.0.0.1
```

where SSH_PORT is the SSH port the container listens on. In my docker run personal example, it's 5022.

Note that X forwarding is known to have issues on Macs.

### Logging in with VNC

Please use `vncviewer`

```
$ vncviewer HOST::VNC_PORT
```

where:
- HOST is the IP address of the host running the container. Example: 127.0.0.1
- VNC_PORT is the VNC port the container forwards. In my docker run example, it's 5900.


## Misc info

### Tools

Android reverse engineering tools are installed in **/opt**.

### Emulators

3 different emulators are installed by default in the container. Quick aliases are available in the `~/.bashrc` to run them.

| Android version | API version | Bashrc alias |
| -------------------- | --------------- | ---------------- |
| Android 9.0 for x86_64    | 28 | emulator9 |
| Android 7.1.1  (Nougat)  | 25 | emulator7 |
| Android 6.0 (Marshmallow) | 23 | no alias |
| Android 5.1 (Lollipop) | 22 | emulator |

So, for example, to run the Android 7.1.1 emulator, do:
```
$ emulator7 &
```
which equivalent to:
```
$ /opt/android-sdk-linux/tools/emulator -avd Android711 -no-audio -no-boot-anim &
```

In doubt, list AVDs using `/opt/android-sdk-linux/tools/bin/avdmanager list avd`

### Android x86_64 emulator

The "normal" Android emulators emulate ARM architecture. If your host uses Intel x86 and supports hardware virtualization instructions, you can use the Android emulator for x86, which will be **much faster**. The Dockerfile installs the necessary packages, yet, for this option to work, you must:

- Have an Intel x86-64 processor on your host which supports virtualization (e.g Intel VT)
- Launch the container with the `--privileged` option.

# Customization

Please change the default password in the `Dockerfile` for your own setup.
Any other customization consists in modifying the `Dockerfile` and re-building your own image. Enjoy!

You are welcome to post issues or suggestions.

# Using [frida](https://www.frida.re/)

Only the part on the Linux host is installed.
You need to push the frida server to the Android emulator.

```bash
$ adb push /opt/frida-server /data/local/tmp
$ adb shell "chmod 755 /data/local/tmp/frida-server"
$ adb shell
1|root@generic:/data/local/tmp # ./frida-server
```

# Workshops

This docker image has been used in several workshops (Hack.lu, Insomnihack, Nuit du Hack, GreHack).

Workshop *samples* are provided to participants by other means.
This image **does not provide any Android sample**.

# Digest

sha256: 7bab7e589d363af35f590ced9483c8f7ae808a42dc6d53944310818fd89a4b09


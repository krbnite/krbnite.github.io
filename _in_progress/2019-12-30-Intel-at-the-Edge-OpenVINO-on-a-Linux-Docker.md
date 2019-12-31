---
title: Intel at the Edge (OpenVINO on a Linux Docker)
layout: post
tags: edge ai easi
---

https://docs.docker.com/engine/reference/builder/

```
ARG <name>[=<default value>]
```

"The ARG instruction defines a variable that users can pass at build-time to the builder with the docker build 
command using the --build-arg <varname>=<value> flag. If a user specifies a build argument that was not defined 
in the Dockerfile, the build outputs a warning."


```
# Set a Single Environment Var 
ENV <key> <value>

# Set Multiple Environment Vars
ENV <key1>=<value1> <key2>=<value2> ...
```

"The ENV instruction sets the environment variable <key> to the value <value>. This value will be in the environment 
for all subsequent instructions in the build stage and can be replaced inline in many as well."


```
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"] # this form is required for paths containing whitespace
```

"The ADD instruction copies new files, directories or remote file URLs from <src> and adds them to the 
filesystem of the image at the path <dest>.  Multiple <src> resources may be specified but if they are files or
directories, their paths are interpreted as relative to the source of the context of the build. ...  The 
<dest> is an absolute path, or a path relative to WORKDIR, into which the source will be copied inside the 
destination container."

```
ADD test relativeDir/          # adds "test" to `WORKDIR`/relativeDir/
ADD test /absoluteDir/         # adds "test" to /absoluteDir/
```


```
WORKDIR /path/to/workdir
```

"The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD 
instructions that follow it in the Dockerfile. If the WORKDIR doesn’t exist, it will be created even if 
it’s not used in any subsequent Dockerfile instruction."


[StackOverflow: Privileged containers and capabilities](https://stackoverflow.com/questions/36425230/privileged-containers-and-capabilities):  "The --privileged flag gives all capabilities to the container, and it also lifts all the limitations enforced by the device cgroup controller. In other words, the container can then do almost everything that the host can do. This flag exists to allow special use-cases, like running Docker within Docker."

[Docker Docs](https://docs.docker.com/engine/reference/run/):  
* network=host: "With the network set to host, a container will share the host's network stack and all interfaces from the host will be available to the container. The container’s hostname will match the hostname on the host system... It is recommended to run containers in this [host] mode when their networking performance is critical, for example, a production Load Balancer or a High Performance Web Server... NOTE: --network="host" gives the container full access to local system services such as D-bus and is therefore considered insecure."
* Detached Mode: "To start a container in detached mode, you use -d=true or just -d option. By design, containers started in detached mode exit when the root process used to run the container exits, unless you also specify the --rm option. If you use -d with --rm, the container is removed when it exits or when the daemon exits, whichever happens first."

-----------------

Here is the Guzman/U-Net mish-mash I was originally trying to get to work:

```docker
## TO BUILD CONTAINER:
##   1. Make sure you have downloaded the Linux version of OpenVINO: 
##        https://software.intel.com/en-us/openvino-toolkit/choose-download/free-download-linux
##   2. Place the downloaded OpenVINO installer in the same directory as this Dockerfile.
##   3. Build the Docker:
##       docker build -t openvino .

##
## TO RUN BUILT CONTAINER:
##   1. For Neural Compute Stick 2
##       docker run \
##           --net=host \
##           -e DISPLAY=$DISPLAY \
##           -v /tmp/.X11-unix:/tmp.X11-unix \
##           --privileged \
##           -v /dev:/dev \
##           -it \
##           openvino
##   2. For CPU
##       docker run \
##           -e DISPLAY=$DISPLAY \
##           -v /tmp/.X11-unix:/tmp.X11-unix  \
##           -it \
##           unet_openvino
##

# The various Dockers I looked at use ubuntu 16.04, but the Intel website
# recommends 18.04.x.
# Ref: https://docs.openvinotoolkit.org/2019_R3.1/_docs_install_guides_installing_openvino_linux.html
#FROM ubuntu:16.04
FROM ubuntu:18.04

ARG OPENVINO_DIR=/opt/intel/openvino

ENV APP_DIR /app
ADD . ${APP_DIR} 
WORKDIR ${APP_DIR}

ENV PATH /opt/conda/bin:$PATH

# Make sure programs are installed
#  -- I added apt-utils to this list since the original build got
#     so many messages like "pkg-X: delaying package configuration, since apt-utils is not installed"
RUN apt-get update 
RUN apt-get install -y --no-install-recommends \
        apt-utils \
        autoconf \
        build-essential \
        curl \
        cpio \
        cmake \
        git \
        g++ \
        libomp-dev \
        libtool \
        lsb-release \
        nano \
        pciutils \
        python3.5 \
        python3-pip \
        python3-setuptools \
        qt5-qmake qtcreator qt5-default \
        sudo \
        tar \
        udev \
        unzip \
        usbutils \
        wget \
        libgtk2.0-dev \
        libcanberra-gtk-module \
        libgflags-dev \
        vim \
        && apt-get clean all



# Install miniconda
RUN wget --quiet \
    https://repo.anaconda.com/miniconda/Miniconda3-4.5.11-Linux-x86_64.sh \
    -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    /opt/conda/bin/conda clean -tipsy && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

RUN conda update -y -n base -c defaults conda


# Upgrade Pip...
#  -- had a warning message: You are using pip version 8.1.1, however version 19.3.1 is available.
#  -- Update: lol, still get that message at one point...so, meh.
#### NOTE: just moved this to below the conda installation, so maybe it'll help....
RUN pip install --upgrade pip


# We need a python environment for OpenVINO
## -- removed this b/c it seems unnecessary (this Docker is specifically for OpenVINO)
RUN conda create -y -n openvino python=3.6 pip h5py numpy matplotlib tensorflow keras requests networkx
RUN conda init bash && conda activate openvino
RUN echo "conda activate openvino" >> ~/.bashrc
#RUN conda install -y pip h5py numpy matplotlib tensorflow keras
# Also adding requests and networkx since I had to do that in the Docker
#RUN conda install -y requests networkx

# Unzip the OpenVINO installer
RUN cd ${APP_DIR} && tar -xvzf l_openvino_toolkit*


# Installing OpenVINO itself
# -- NOTE:  In Intel's U-Net Dockerfile, this came after installing OpenVINO
#    dependencies, but on the official installation guide this comes first;
#    since I had issues with the original Dockerfile, I'm putting this here
#    to more strictly follow the guide
RUN cd ${APP_DIR}/l_openvino_toolkit* && \
    sed -i 's/decline/accept/g' silent.cfg && \
    ./install.sh --silent silent.cfg
    
# Installing OpenVINO dependencies
# -- NOTE:  In official installation guide, this file is called from
#    /opt/intel/openvino/install_dependencies; I've checked the file at
#    both locations: they're the same file
RUN cd ${APP_DIR}/l_openvino_toolkit* && \
    ./install_openvino_dependencies.sh


# OpenVINO Env Vars
# -- comes before Model Optimizer stuff in Installation Guide
RUN /bin/bash -c "source $OPENVINO_DIR/bin/setupvars.sh"
RUN echo "source ${OPENVINO_DIR}/bin/setupvars.sh" >> ~/.bashrc
    
    
# Model Optimizer
#  -- added from Mateo Guzman's Docker
#  -- maybe all this is done in above apt-get commands, but
#     just to make sure...
RUN cd $OPENVINO_DIR/deployment_tools/model_optimizer/install_prerequisites && \
    ./install_prerequisites.sh


# Build the samples so that we have libraries
#  -- this is the line in the U-Net Docker that failed me
#  -- to fix this, I added the "source $OPENVINO_DIR/bin/setupvars.sh" line above
#  .....to no avail: still crashing!
#  Commenting it out for now....
#RUN /bin/bash -c "${OPENVINO_DIR}/inference_engine/samples/build_samples.sh"


# USB rules for Myriad
RUN cp ${APP_DIR}/97-myriad-usbboot.rules /etc/udev/rules.d/
RUN echo "udevadm control --reload-rules" >> ~/.bashrc
RUN echo "udevadm trigger" >> ~/.bashrc


# Cleanup
#  -- autoremove from Guzman (everything else from Intel)
RUN apt autoremove -y
RUN rm -rf ${APP_DIR}/l_openvino_toolkit*
RUN rm -f ${APP_DIR}/Dockerfile

CMD ["/bin/bash"]
```


------------------------------


# Docker Diskspace Overload: the Need to Clean Up!
So many of my builds didn't work for this reason or that.  Other times, when a build was successful, the OpenVINO
demos wouldn't work...  Then, at one point, I couldn't build at all!

I began receiving error messages, like:

```
W: Failed to fetch http://archive.ubuntu.com/ubuntu/dists/bionic/InRelease  
Error writing to output file - write (28: No space left on device) [IP: 91.189.88.24 80]

W: Some index files failed to download. They have been ignored, or old ones used instead.
```

I realized that a lot of my failed builds are probably somehow taking up space that Docker
has allotted for its images...  Maybe as storage.  Maybe as processes that have continued
running in the background...  So I looked at running processes.

```
docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                       PORTS               NAMES
dac12cb791e3        32b98b59a557        "/bin/sh -c 'apt-get…"   3 minutes ago       Exited (100) 3 minutes ago                       competent_chebyshev
e362989ff5ff        32b98b59a557        "/bin/sh -c 'apt-get…"   9 minutes ago       Exited (100) 9 minutes ago                       ecstatic_shockley
3e90feca5a5f        97b93b623da2        "/bin/sh -c 'conda i…"   45 minutes ago      Exited (0) 29 minutes ago                        stoic_varahamihira
213700fd87a2        08c1d8e8b2c7        "/bin/bash"              55 minutes ago      Exited (0) 54 minutes ago                        naughty_antonelli
cc04076ab812        ubuntu              "/bin/bash"              58 minutes ago      Exited (1) 55 minutes ago                        adoring_rhodes
3ff69f2508c4        ubuntu              "/bin/bash"              58 minutes ago      Exited (0) 58 minutes ago                        wizardly_jang
32b943a47f72        08c1d8e8b2c7        "/bin/bash"              About an hour ago   Exited (0) 59 minutes ago                        exciting_torvalds
```

The list actually went on quite a bit...  So I removed all recent stuff, e.g.:

```
docker image rm 32b98 -f
```

I also looked at the images on disk and removed some stuff:

```
docker image ls
  REPOSITORY                                                                                    TAG                 IMAGE ID            CREATED             SIZE
  <none>                                                                                        <none>              827f55570a00        2 hours ago         571MB
  <none>                                                                                        <none>              b4a1dc58e528        14 hours ago        7.23GB
  <none>                                                                                        <none>              f6683efde4d1        14 hours ago        1.49GB
  <none>                                                                                        <none>              4049958496ec        15 hours ago        7.22GB
  <none>                                                                                        <none>              2ccad03d503c        21 hours ago 
```

I removed a bunch of that stuff manually...but got another error when I went to build.

A quick google search brought me some relief: [Docker - no space left on device MacOS](https://stackoverflow.com/questions/48668660/docker-no-space-left-on-device-macos_)

This helped!

```
docker rmi -f $(docker images | grep "<none>" | awk "{print \$3}")
docker rm -f $(docker ps -aq)  # this removes all containers...so you've been warned
```



-----------------------------------------------------------

# Docker's Automatic Unzipping
At one point, I gave up on trying to combine the "best" of the Guzman and U-Net dockers... You
might wonder why I was trying to do that anyway... Partially, it was because the U-Net docker failed to 
build by itself.  Combine that with the observation that the U-Net Dockerfile didn't include some of the steps
that the Guzman Docker did, and it got me thinking that I could fix the U-Net Dockerfile.  I initially wasn't 
interested in trying to run the Guzman Docker because it didn't include the Miniconda version of Python;
from my understanding, Anaconda provides Intel-optimized version of TensorFlow by default (see:
[Intel Optimization for TensorFlow Installation Guide](https://software.intel.com/en-us/articles/intel-optimization-for-tensorflow-installation-guide)).

Anyway, after a lot of twists, turns, and winding roads to failure, I decided to try out
Guzman's Dockerfile...but, again, with a few tweaks that cost me more time.  The major tweak
was wanting to untar/unzip the OpenVINO tgz file within the Dockerfile, like done in
the U-Net Dockerfile, whereas the Guzman Dockerfile assumed it was already uncompressed in
the Dockerfile's directory.

This got me tripped up for a bit!  

Basically, Docker has some slightly weird behaviors around automatic unzipping
of zipped/tarred files: 
* sometimes a tgz file is automatically uncompressed, which means trying to uncompress it again will throw an error
* other times a tgz file is not automatically uncompressed, requiring it to be explicitly uncompressed in the Dockerfile

Over and over again, I'd get the same error message:

```docker
Step 5/17 : RUN tar -xvzf /openvino/l_openvino_toolkit*
 ---> Running in 8dbc9c899090
tar (child): /openvino/l_openvino_toolkit_p_2019.3.376: Cannot read: Is a directory
tar (child): At beginning of tape, quitting now
tar (child): Error is not recoverable: exiting now

gzip: stdin: unexpected end of file
tar: Child returned status 2
tar: Error is not recoverable: exiting now
The command '/bin/sh -c tar -xvzf /openvino/l_openvino_toolkit*' returned a non-zero code: 2
```

I actually figured out this was happened by issuing a `ls` command in the Dockerfile:

```docker
FROM ubuntu:16.04
ADD l_openvino_toolkit* /openvino/
ARG INSTALL_DIR=/opt/intel/openvino 

# Decompress OpenVINO 
RUN ls /openvino/l_openvino_toolkit*
RUN tar -xvzf /openvino/l_openvino_toolkit* 
```

This provided a major hint in the output:

```
Step 2/17 : ADD l_openvino_toolkit* /openvino/
 ---> 62f3fd8a74e6
Step 3/17 : ARG INSTALL_DIR=/opt/intel/openvino
 ---> Running in 359c729ae7e7
Removing intermediate container 359c729ae7e7
 ---> 35ed6aa8bac4
Step 4/17 : RUN ls /openvino/l_openvino_toolkit*
 ---> Running in 020c2f658632
EULA.txt
PUBLIC_KEY.PUB
install.sh
install_GUI.sh
install_openvino_dependencies.sh
pset
rpm
silent.cfg
```

In step 2/17, I was adding a .tgz file, but in step 4/17 you can see it's
automatically unzipped...which is why step 5/17 above kept failing.  

Obviously if you've formally learned Docker in and out, this is probably not a big
surprise, but it's been a lesson learned for me.  I confirmed my suspicion in a quick
google search, which led me here: 
* [Dockerfile ADD should support ZIP archives as well](https://github.com/moby/moby/issues/15036)

Weirdly enough, the Intel U-Net version of the Dockerfile uses this untarring command...which is
where I got it from.  

```docker
# Unzip the OpenVINO installer
RUN cd ${APP_DIR} && tar -xvzf l_openvino_toolkit*
```

When I build this Dockerfile, this step works just fine... The only difference seems to
be in the way the `ADD` the tar file:

```docker
ENV APP_DIR /app
ADD . ${APP_DIR}
```

Basically, the `.` wildcard seems to skirt around the automatic unzipping...  

Drum roll, please... Yes!  This is the case.  The following change to the Dockerfile
fixes everything:

```
FROM ubuntu:16.04
##ADD l_openvino_toolkit* /openvino/
# -- Use '.' instead --
ADD . /openvino/    
```

----------------------------------------------------------

# A Working Dockerfile!


Got some warnings/errors at Step 13/17 (`RUN cd $INSTALL_DIR/deployment_tools/model_optimizer/install_prerequisites &&     ./install_prerequisites.sh`), but this Dockerfile works...   (Note: this is basically
Guzman's Docker w/ very minor tweaks.)

Warnings/Errors:
```
ERROR: tensorboard 1.15.0 has requirement setuptools>=41.0.0, but you'll have setuptools 39.0.1 which is incompatible.
ERROR: mxnet 1.3.1 has requirement numpy<1.15.0,>=1.8.2, but you'll have numpy 1.18.0 which is incompatible.
```

Dockerfile:
```docker
FROM ubuntu:18.04

ADD . /openvino/

ARG INSTALL_DIR=/opt/intel/openvino 

# Decompress OpenVINO 
RUN cd /openvino && \
    tar -xvzf l_openvino_toolkit* 
RUN rm /openvino/*tgz

# apt-get stuff
RUN apt-get update && apt-get -y upgrade && apt-get autoremove -y

#Install needed dependences
RUN apt-get install -y --no-install-recommends \
        build-essential \
        cpio \
        curl \
        git \
        lsb-release \
        pciutils \
        python3 \
        python3-dev \
        python3-pip \
        python3-setuptools \
        sudo

# installing OpenVINO dependencies
RUN cd /openvino/l_openvino_toolkit* && \
    ./install_openvino_dependencies.sh

RUN pip3 install numpy
RUN pip3 install --upgrade pip
RUN pip install --upgrade pip

# installing OpenVINO itself
RUN cd /openvino/l_openvino_toolkit* && \
    sed -i 's/decline/accept/g' silent.cfg && \
    ./install.sh --silent silent.cfg

# Model Optimizer
RUN cd $INSTALL_DIR/deployment_tools/model_optimizer/install_prerequisites && \
    ./install_prerequisites.sh

# clean up 
RUN apt autoremove -y && \
    rm -rf /openvino /var/lib/apt/lists/*

RUN /bin/bash -c "source $INSTALL_DIR/bin/setupvars.sh"

RUN echo "source $INSTALL_DIR/bin/setupvars.sh" >> /root/.bashrc

CMD ["/bin/bash"]
```

### Squeezenet Demo: Success!
I can run squeezenet demo by simply starting an interactive session:
```
docker run -it openvino
```

### Security Demo: Fail!
However, the security demo requires access to a display...  If you remember, Intel's
U-Net docker recommended spinning it up like so:
```
docker run --net=host -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp.X11-unix --privileged -v /dev:/dev -it openvino
```

But even this results in an error (last few lines of security output):
```
[ INFO ] Resizable input with support of ROI crop and auto resize is disabled
Unable to init server: Could not connect: Connection refused

(Detection results:3388): Gtk-WARNING **: 23:32:41.904: cannot open display: /private/tmp/com.apple.launchd.qmVw5QAfGM/org.macosforge.xquartz:0
Error on or near line 198; exiting with status 1
```

### Security Demo:  GUI Success!  
Fortunately, people have solved this problem -- e.g.:
* [Docker for Mac and GUI applications](https://fredrikaverpil.github.io/2016/07/31/docker-for-mac-and-gui-applications/)
* [Running GUI applications using Docker for Mac](https://sourabhbajaj.com/blog/2017/02/07/gui-applications-docker-mac/)

The gist from both those sources is this:
* Make sure you have XQuartz installed (e.g., `brew cask install XQuartz`)
* Open XQuartz:  `open -a XQuartz`
* Ensure that connections can be made to XQuartz
  - XQuartz > Preferences... > Security
  - Check off "Allow connections from network clients"
* Get your machine's IP:  `IP=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')`
* Add you machine's IP to the xhost list: `xhost + $IP`
* Now you can run a GUI from a docker -- in our case, the openvino docker: 
  - here is the way recommended by Intel's U-Net docker (slightly modified: DISPLAY=$DISPLAY --> DISPLAY=${IP}:0): `docker run --net=host -e DISPLAY=${IP}:0 -v /tmp/.X11-unix:/tmp.X11-unix --privileged -v /dev:/dev -it openvino`
  - you can cut that down though: `docker run -e DISPLAY=${IP}:0 -v /tmp/.X11-unix:/tmp.X11-unix -it openvino`
  - finally, run the security demo: `cd /opt/intel/openvino/deployment_tools/demo && ./demo_security_barrier_camera.sh`

**Some Troubleshooting**: This actually didn't work for me at first.  I already had XQuartz installed, so
I didn't bother brew installing it.  This was frustrating, but source after source gave the same 
general guidelines...so I finally figured I'd try `brew cask reinstall xquartz`.  Things still weren't
working, though I'm not sure if this is because I was all wound up in a brain tornado.  Finally, I
just uninstalled, then reinstalled (`brew cask uninstall xquartz; brew cask install xquartz`), and
everthing worked thereafter.

**Instability**: one thing I did notice is that the XQuartz display from the security demo was highly
unstable.  Touching almost any key seemed to make it vanish.  But, hey, a win's a win -- and this here
felt like a win! :-)



----------------------------------------------------------

# References and Further Reading
* [OpenVINO Linux Installation Guide](https://docs.openvinotoolkit.org/2019_R3.1/_docs_install_guides_installing_openvino_linux.html)
* [Intel's OpenVINO/NCS2 U-Net Docker](https://github.com/IntelAI/unet/tree/master/2D/docker)
* [Mateo Guzman's OpenVINO Docker](https://github.com/mateoguzman/openvino-docker/blob/master/Dockerfile)
* [Intel's Open Model Server Docker](https://github.com/IntelAI/OpenVINO-model-server/blob/master/docs/docker_container.md)
* [Docker - no space left on device MacOS](https://stackoverflow.com/questions/48668660/docker-no-space-left-on-device-macos_)
* [Dockerfile ADD should support ZIP archives as well](https://github.com/moby/moby/issues/15036)
* [Intel Optimization for TensorFlow Installation Guide](https://software.intel.com/en-us/articles/intel-optimization-for-tensorflow-installation-guide)
* [Docker for Mac and GUI applications](https://fredrikaverpil.github.io/2016/07/31/docker-for-mac-and-gui-applications/)
* [Running GUI applications using Docker for Mac](https://sourabhbajaj.com/blog/2017/02/07/gui-applications-docker-mac/)
* [StackOverflow: Privileged containers and capabilities](https://stackoverflow.com/questions/36425230/privileged-containers-and-capabilities)
* [Docker Docs: Builder](https://docs.docker.com/engine/reference/builder/)
* [Docker Docs: Run](https://docs.docker.com/engine/reference/run/)

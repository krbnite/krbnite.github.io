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



# Docker Clean-Up
My builds didn't work for this reason or that.  Sometimes when a build was successful, the OpenVINO
demos wouldn't work...  At one point, I couldn't build at all:  I began receiving error messages, like:

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

I removed a bunch of that stuff manually...but got another error when I went to build:

```

```

So, [Docker - no space left on device MacOS](https://stackoverflow.com/questions/48668660/docker-no-space-left-on-device-macos_

This helped!

```
docker rmi -f $(docker images | grep "<none>" | awk "{print \$3}")
docker rm -f $(docker ps -aq)  # this removes all containers...so you've been warned
```


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


For NCS2, we add the following....

```docker
# USB rules for Myriad
RUN cp ${APP_DIR}/97-myriad-usbboot.rules /etc/udev/rules.d/
RUN echo "udevadm control --reload-rules" >> ~/.bashrc
RUN echo "udevadm trigger" >> ~/.bashrc

# Cleanup
RUN rm -f ${APP_DIR}/97-myriad-usbboot.rules
```

-----------------------------------------------------------

# Docker's Automatic Unzipping
Something that got me tripped up for a bit is Docker's automatic unzipping
of zipped/tarred files.  Over and over again, I'd get the same error message:

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

# LSUSB
To get things working on MacOS, all the installation guide for MacOS says is that you
will need to install `libusb`.  This didn't seem to do anything for me...  Then I accidentally
brew installed `lsusb` when I was tinkering around.  This at least provided a sanity check:
the NCS2 was indeed being recognized by my Mac.

```
brew install lsusb
```

You can then see if you NCS2 is recognized:
```
lsusb | grep Movidius
  Bus 020 Device 010: ID xxxx:xxxx xxxx Movidius MyriadX  Serial: xxxxxxxx
```

I then tried to run a demo to see what happens:
```
cd /opt/intel/openvino/deployment_tools/demos/
sudo ./demo_squeezenet_download_convert_run.sh -d MYRIAD
```

However, as expected, this crashes... Here's the last few lines of output:
```
Run ./classification_sample_async \
  -d MYRIAD \
  -i /opt/intel/openvino_2019.3.376/deployment_tools/demo/car.png \
  -m /Users/kevinurban/openvino_models/ir/public/squeezenet1.1/FP16/squeezenet1.1.xml

[ INFO ] InferenceEngine: 
	API version ............ 2.1
	Build .................. 32974
	Description ....... API
[ INFO ] Parsing input parameters
[ INFO ] Parsing input parameters
[ INFO ] Files were added: 1
[ INFO ]     /opt/intel/openvino_2019.3.376/deployment_tools/demo/car.png
[ INFO ] Creating Inference Engine
	MYRIAD
	myriadPlugin version ......... 2.1
	Build ........... 32974

[ INFO ] Loading network files
[ INFO ] Preparing input blobs
[ WARNING ] Image is resized from (787, 259) to (227, 227)
[ INFO ] Batch size is 1
[ INFO ] Loading model to the device
E: [ncAPI] [    950632] [] ncDeviceOpen:859	Device doesn't appear after boot
[ ERROR ] Can not init Myriad device: NC_ERROR
Error on or near line 217; exiting with status 1
```

Oddly, once you do this, the NCS2 info changes in the `lsusb` output:

```
Bus 020 Device 011: ID zzz:zzzz zzzz VSC Loopback Device  Serial: zzzzzzzzzzzzzzzz
```

You can guess that this is true by the process of elimination (the other USB devices listed
remain the same), and further by googline "VSC Loopback Device".  


----------------------------------------

# LIBUSB


----------------------------------------------------------

# References and Further Reading
* [Intel's OpenVINO/NCS2 U-Net Docker](https://github.com/IntelAI/unet/tree/master/2D/docker)
* [Mateo Guzman's OpenVINO Docker](https://github.com/mateoguzman/openvino-docker/blob/master/Dockerfile)
* [Intel's Open Model Server Docker](https://github.com/IntelAI/OpenVINO-model-server/blob/master/docs/docker_container.md)
* JWRR blog: Good posts on trials and errors of getting NCS2 to work
  - http://www.jwrr.com/ncs2-1/
  - http://www.jwrr.com/ncs2-2/
  

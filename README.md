# SCRIMMAGE Multi-Agent Simulator

[![Build Status](https://travis-ci.org/gtri/scrimmage.png?branch=master)](https://travis-ci.org/gtri/scrimmage)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/gtri/scrimmage/issues)

![SCRIMMAGE Logo](./docs/source/images/scrimmage_vert_black_clean_medium.png)

## Build SCRIMMAGE

### Install Binary Dependencies

A list of the Ubuntu packages required is provided in
./setup/install-binaries.sh in the "DEPS_DPKG" array. Run our automated
installer to install the required packages:

    $ sudo ./setup/install-binaries.sh

### Build Dependencies from Source

Some of the dependencies need to be built and installed from source. The
following commands will build protobuf, grpc, and jsbsim from source and
install them to ~/.local by default:

    $ cd 3rd-party
    $ mkdir build && cd build
    $ cmake ..
    $ source ~/.scrimmage/setup.bash
    $ make
    $ cd ../../

### Build SCRIMMAGE Core

    $ mkdir build
    $ cd build
    $ cmake ..
    $ source ~/.scrimmage/setup.bash
    $ make

### Environment Setup

Whenever, you want to use scrimmage, you need to source the
~/.scrimmage/setup.bash file or you can place a line in your ~/.bashrc file to
source it automatically:

    $ echo "source ~/.scrimmage/setup.bash" >> ~/.bashrc

## Run SCRIMMAGE

Open a new terminal, change to the scrimmage directory, and execute a mission.

    $ cd scrimmage
    $ scrimmage ./missions/straight.xml

You should see the visualization GUI open up and display the simulation.

## GUI Commands

The GUI responds to the following input keys:

    'q'                     : Quit the simulation
    'b'                     : (Break) Pauses and unpauses the simulation.
    'space bar'             : When paused, take a single simulation step.
    'a'                     : Rotate through the camera views
    'right/left arrows'     : Change the aircraft to follow
    '['                     : Decrease simulation warp speed
    ']'                     : Increase simulation warp speed
    '+'                     : Increase visual scale of all entities
    '-'                     : Decrease visual scale of all entities
    'r'                     : Reset visual scale and reset camera position
    'z'                     : Zoom out from entity
    'Z'                     : Zoom in to entity (z+shift)
    'w'                     : Display wireframe
    's'                     : Display solids (vs. wireframe)
    'CTRL + Left Click'     : Rotate world
    'SHIFT + Left Click'    : Translate camera through world

The GUI's camera can operate in three modes (cycle with 'a' key):
1. Follow the entity and point towards the entity's heading
2. Free floating camera
3. Follow the entity from a fixed viewpoint

Note: If all of the terrain data does not appear, click on the GUI window with
your mouse.

## Build SCRIMMAGE Documentation

    $ cd build
    $ cmake .. -DBUILD_DOCS=ON
    $ make docs

### View SCRIMMAGE API (Doxygen) Documentation

    $ firefox ./docs/doxygen/html/index.html

### View SCRIMMAGE Tutorial (Sphinx) Documentation

    $ firefox ./docs/sphinx/html/index.html

## Cleaning SCRIMMAGE

The scrimmage source code can be cleaned with the standard clean command:

    $ make clean

However, if you want to clean everything, you can remove your build directory:

    $ cd /path/to/scrimmage && rm -rf build

## Python Bindings

SCRIMMAGE's Python bindings depend on protobuf, GRPC, and pandas. We recommend
that you use the protobuf and grpc python packages built from our project.

In the SCRIMMAGE Core CMake project, to specify a particular Python verision,
you can use the Python\_ADDITIONAL\_VARIABLES cmake argument, e.g.

    $ cmake .. -DPython_ADDITIONAL_VERSIONS=3.5

Otherwise, cmake will choose on its own.

### Install protobuf Python package

Protobuf should be installed using the source files that are compiled during
build.  Using protobuf from PyPI (i.e. what you get with `pip install protobuf`
) is known to cause crashes.

    $ cd /path/to/scrimmage/3rd-party/build/src/protobuf/python
    $ python setup.py build
    $ sudo python setup.py install

### Build GRPC Python Bindings

    $ cd /path/to/scrimmage/3rd-party/build/src/grpc
    $ sudo pip install -rrequirements.txt
    $ GRPC_PYTHON_BUILD_WITH_CYTHON=1 sudo python setup.py install

If you are using python 3, make sure the futures package isn't installed.

    $ sudo pip3 uninstall futures

### Install SCRIMMAGE Python Bindings

To install scrimmage's python bindings:

    $ cd /path/to/scrimmage/python
    $ sudo pip install -e .

## MOOS Integration

If you want to use MOOS with SCRIMMAGE, you will first need to download and
build MOOS/MOOS-IVP according to the instructions at:
http://oceanai.mit.edu/moos-ivp/pmwiki/pmwiki.php?n=Site.Download

The MOOSAutonomy plugin interacts with the MOOSDB to synchronize time, exchange
contact information, and receive desired state from the IvP Helm. To build
MOOSAutonomy, you have to provide cmake with the path to the moos-ivp source
tree:

    cmake .. -DMOOSIVP_SOURCE_TREE_BASE=/path/to/moos-ivp

## Obtain Pre-Built SCRIMMAGE Docker Image

The SCRIMMAGE docker image isn't publicly available yet, but for GTRI
employees, they can use the internal docker registry.

1. Allow non-https connections for your docker client. Add the following
   information to your /etc/docker/daemon.json file:

        {
            "dns": ["10.104.30.150", "8.8.8.8"],
            "insecure-registries" : ["10.108.21.229:5000"]
        }

    Reference:
    https://docs.docker.com/registry/insecure/

2. Restart docker service:

        $ sudo service docker restart

3. Pull the image:

        $ docker pull 10.108.21.229:5000/scrimmage:latest

4. Run scrimmage (without GUI):

        $ docker run -it 10.108.21.229:5000/scrimmage:latest /bin/bash
        $ scrimmage ./missions/straight-no-gui.xml


## Installing and Configuring Open Grid Engine

Instructions modified from:
    https://scidom.wordpress.com/2012/01/18/sge-on-single-pc/
    http://www.bu.edu/tech/support/research/system-usage/running-jobs/tracking-jobs/

Install Grid Engine:

    $ sudo apt-get install gridengine-master gridengine-exec \
      gridengine-common gridengine-qmon gridengine-client

Note that you can configure how qsub is called with a `.sge_request` in your
home directory. Further, you can set the number of available slots (cores
available) when running grid engine under the Queue Control tab.

## Installing, Configuring, and Using Docker

1. Setup an openssh server

    sudo apt install openssh-server

2. Generate keys somewhere (i.e., override the default path to the ssh key)

    ssh-keygen

3. Add the new key to the authorized keys

    cat /path/to/new/id_rsa.pub >> ~/.ssh/authorized_keys
    sudo service ssh restart

4. Get your ip address

    nmap -p 22 localhost

5. Build docker images

    cd /path/to/scrimmage/ci
    ./build.sh ./ubuntu16.04
    ./build.sh ./ubuntu14.04

6. You can then step into the image with

    docker run -i -t scrimmage:16.04 /bin/bash

7. Docker troubleshooting:
Docker can have DNS issues. If you can ping a public ip address within a docker
image (such as 8.8.8.8), but you can't ping archive.ubuntu.com, create the file
/etc/docker/daemon.json with the following contents:

    {
        "dns": ["<DNS-IP>", "8.8.8.8"]
    }

Where <DNS-IP> is the first DNS IP address from the command:

    nmcli dev list | grep 'IP4.DNS'          # Ubuntu <= 14
    nmcli device show <interfacename> | grep IP4.DNS   # Ubuntu >= 15

Restart docker:

    sudo service docker restart

## Troubleshooting

### Problem: I cannot load python libraries through scrimmage

Make sure that when you run the cmake command it is using the version of python
that you want to use with the following:

    $ cmake -DPYTHON_EXECUTABLE:FILEPATH=/usr/bin/python      \  # adjust path to your needs
            -DPYTHON_INCLUDE_DIR:PATH=/usr/include/python2.7  \  # adjust path to your needs
            -DPYTHON_LIBRARY:FILEPATH=/usr/lib/libpython2.7.so   # adjust path to your needs

### Problem: vtkRenderingPythonTkWidgets cmake Warning

When running cmake, the user gets the cmake warning:

    -- The imported target "vtkRenderingPythonTkWidgets" references the file
    "/usr/lib/x86_64-linux-gnu/libvtkRenderingPythonTkWidgets.so"
    but this file does not exist.  Possible reasons include:
    * The file was deleted, renamed, or moved to another location.
    * An install or uninstall procedure did not complete successfully.
    * The installation package was faulty and contained
    "/usr/lib/cmake/vtk-6.2/VTKTargets.cmake"
    but not all the files it references.

This is a VTK6 Ubuntu package bug. It can be ignored.
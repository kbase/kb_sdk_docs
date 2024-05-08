
Dependencies & Prerequisites
==================================

System Dependencies:

* Mac OS X 10.8+ (Docker requires this) or Linux.  kb-sdk does not run natively in Windows
* Xcode (Mac only) |Xcode_link|
* git |gitscm_link|
* Docker runtime |docker_link|
    * Attention: Docker Desktop is banned at ANL and possibly other institutions.
    * Additional information about Docker Desktop restrictions at ANL: |docker_restrictions_link|
    * Rancher Desktop |rancherdesktop_link| (alternative to Docker Desktop)
* At least 20 GB free on your hard drive to install Docker, Xcode, Java JRE, etc.
* (Not required for Docker-based installation) Java JRE 7 or 8 (9 is currently incompatible) |JAVAjre_link|

We recommend using Mac OS X 10.8+ or Linux for SDK development. Windows development is not currently supported.  If you are running Windows or do not want to develop on your local machine, we recommend using |virtualbox_link| and installing Ubuntu 14+.

Xcode or the Xcode Command Line Tools will install some standard terminal commands like `make` and `git` that are necessary for building the KBase SDK and your module code.  |Xcode_link|

The git homepage can be found at |gitscm_link|. On Ubuntu, install it with ``sudo apt-get install git``.

Docker Installation
---------------------

KBase module code is run using Docker, which allows you to easily install all system tools and dependencies your module requires. Installing Docker locally allows you to test your build and run tests on your own computer before registering your module with KBase.

Instructions for installing on Mac: |dockermac_link|

Instructions for installing on Linux: |dockerlinux_link|

On Linux, be sure to follow these |postInstall_link| so you can run docker and `kb-sdk` as a non-root user.

When you run an app in a narrative, it runs in a docker container on KBase's servers. Learn more about docker containers here: |whatContainer_link|. Docker containers allow you to package and compile programs and dependencies, such as  |megahit_link|, and run them anywhere.


.. External links

.. |JAVAJre_link| raw:: html

   <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">http://www.oracle.com/technetwork/java/javase/downloads/index.html</a>

.. |Xcode_link| raw:: html

   <a href="https://developer.apple.com/xcode" target="_blank">https://developer.apple.com/xcode</a>

.. |gitscm_link| raw:: html

   <a href="https://git-scm.com" target="_blank">https://git-scm.com</a>

.. |docker_link| raw:: html

   <a href="https://www.docker.com" target="_blank">https://www.docker.com</a>

.. |rancherdesktop_link| raw:: html

   <a href="https://docs.rancherdesktop.io/" target="_blank">https://docs.rancherdesktop.io/</a>

.. |docker_restrictions_link| raw:: html

   <a href="https://help.cels.anl.gov/2023/08/16/docker-desktop-is-no-longer-allowed-at-argonne/" target="_blank">https://help.cels.anl.gov/2023/08/16/docker-desktop-is-no-longer-allowed-at-argonne/</a>

.. |virtualbox_link| raw:: html

   <a href="https://www.virtualbox.org" target="_blank">Virtualbox</a>

.. |dockermac_link| raw:: html

   <a href="https://docs.docker.com/docker-for-mac/install/" target="_blank">https://docs.docker.com/docker-for-mac/install/</a>

.. |dockerlinux_link| raw:: html

   <a href="https://docs.docker.com/install/linux/docker-ce/ubuntu/" target="_blank">https://docs.docker.com/install/linux/docker-ce/ubuntu/</a>

.. |postInstall_link| raw:: html

   <a href="https://docs.docker.com/install/linux/linux-postinstall" target="_blank">post installation steps </a>

.. |whatContainer_link| raw:: html

   <a href="https://www.docker.com/what-container" target="_blank">https://www.docker.com/what-container</a>

.. |megahit_link| raw:: html

   <a href="https://github.com/voutcn/megahit" target="_blank">MEGAHIT </a>




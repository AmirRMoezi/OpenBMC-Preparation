# How to prepare OpenBMC
In this tutorial, you will learn how to prepare OpenBMC as an open source embedded Linux for Baseboard management controllers.
<p align="center">
  <img width="300" height="300" src="https://github.com/AmirRMoezi/OpenBMC/blob/main/OpenBMC_logo.png">
</p>

## Step 1
Choose Ubuntu 22.04 LTS as your host. Install it on your computer or a virtual environment such as VMWare. Do not forget to download updates while installing the Ubuntu. After installation process has been completed, check for updates using Software Updater app. then, update the package index files on the system using 'sudo apt-get update' command in terminal.

## Step 2
### Install the requrements:
<pre>
<code>
sudo apt install git python3-distutils gcc g++ make file wget \ 
      gawk diffstat bzip2 cpio chrpath zstd lz4 bzip2 unzip texinfo \
      build-essential  socat  python3 python3-pip python3-pexpect xz-utils \
      debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa \
      libsdl1.2-dev python3-subunit mesa-common-dev  liblz4-tool locales
</code>
</pre>
<pre><code>
sudo locale-gen en_US.UTF-8
</code>
</pre>
## Step 3
### Downlod the OpenBMC source:
git clone https://github.com/openbmc/openbmc
cd openbmc

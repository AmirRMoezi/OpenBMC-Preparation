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
<pre><code>
git clone https://github.com/openbmc/openbmc
cd openbmc
</code>
</pre>
## Step 4
### Target your hardware
It is essential to set up your build environment according to your target hardware. run the following command to get supported taget hardwares
<pre>
<code>
$ . setup

bletchley               mori                    s8036
dl360poc                mtjade                  swift
e3c246d4i               mtmitchell              tatlin-archive-x86
ethanolx                nicole                  tiogapass
evb-ast2500             olympus-nuvoton         transformers
evb-ast2600             on5263m5                vegman-n110
evb-npcm750             p10bmc                  vegman-rx20
f0b                     palmetto                vegman-sx20
fp5280g2                qcom-dc-scm-v1          witherspoon
g220a                   quanta-q71l             witherspoon-tacoma
gbs                     romed8hm3               x11spi
greatlakes              romulus                 yosemitev2
gsj                     s2600wf                 zaius
kudo                    s6q
lannister               s7106
</code>  
</pre>

# How to prepare OpenBMC
In this tutorial, you will learn how to prepare OpenBMC as an open source embedded Linux for AST2500 Evaluation Board.
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

In this tutorial, we use evb-ast2500 as the target. So, run this command in terminal without sudo:

<pre>
<code>
$ . setup evb-ast2500  
</code>  
</pre>

Then, a folder called evb-ast2500 is cerated in the directory ./build. If you have encountered any warning message about not having permission to chmod a file, open a new terminal and change the permission of all files and folders in ./build/evb-ast2500 to 777 with sudo. Then, run the .setup command again in the previous terminal without sudo. At last the output of this command looks like this:
<pre>
<code>
obmc@obmc-virtual-machine:~/Desktop/openbmc$ . setup evb-ast2500
Machine evb-ast2500 found in meta-evb/meta-evb-aspeed/meta-evb-ast2500
WARNING: unable to chmod /home/obmc/Desktop/openbmc/build/evb-ast2500
WARNING: unable to chmod /home/obmc/Desktop/openbmc/build/evb-ast2500/conf
Common targets are:

     obmc-phosphor-image: Includes OpenBMC Phosphor userspace and Web UI

     core-image-minimal: A small image just capable of allowing a device to boot

     core-image-full-cmdline: A small image with more Linux functionality
                              installed, including a ssh server.
</code>  
</pre>

## Step 5
### Bake your OpenBMC
In the same terminal you ran .setup command, run this command:
<pre>
<code>  
$ bitbake obmc-phosphor-image
</code>  
</pre>
Bitbake is a build tool for embedded Linux cross compilation. It compiles your OpenBMC.

## Step 6
### Everything is ready. Run your OpenBMC
After successfully compiling OpenBMC, the images necessary for running OpenBMC on Qemu and real board are ready. The images exist in this directory:
<pre>
<code>
openbmc/build/evb-ast2500/tmp/deploy/images  
</code>  
</pre>

To simulate OpenBMC, you need the image with .mtd extension. Find it and simulate it in qemu using this command:
<pre>
<code>
qemu-system-arm -M ast2500-evb -nic user -drive file=obmc-phosphor-image-ast2500.static.mtd,format=raw,if=mtd -nographic
</code>  
</pre>
  Note that the name of your mtd file may differ. It is probably like 'obmc-phosphor-image-evb-ast2500-20230813043725.static.mtd'. So change the name of mtd file in the command above according to your mtd file name. This image is included it the repository.
To run the OpenBMC on the real AST2500 evaluation board, you should extract a compressed file with '.mtd.all.tar' extension. It includes a file named 'image-bmc'. put a .bin extension on it and program the board with this image. This image is included it the repository.

# Possible problems and solutions
## Do not use Bitbake as root
  You may encounter this error at the beginning of baking process:
<pre>
  <code>
    ERROR:  OE-core's config sanity checker detected a potential misconfiguration.
    Either fix the cause of this error or at your own risk disable the checker (see sanity.conf).
    Following is the list of potential problems / advisories:

    Do not use Bitbake as root.
  </code>
</pre>
It says that yoy should not have root access in the terminal in which you are executing setup and bitbake commands as was said in Step 4.

## Fetcher failure for URL: 'https://yoctoproject.org/connectivity.html'
  This error says that there is a problem in your internet connection or your ISP or network is blocking the above URL. Check it and if it was not solved, add 'CONNECTIVITY_CHECK_URIS = "URL"' to this file :
<pre>
  <code>
    openbmc/poky/meta/conf/sanity.conf
  </code>
</pre>
Replace 'URL' with any URL accesssible from your network. This is the method Bitbake uses to chech internet access.

## Warning: ... do_fetch: Failed to fetch URL ..., attemting mirrors if available
  This warning indicates that there is a problem in downloading some files from the URL mentioned in warning the message because of internet connection problems. It tries to fetch it from mirror links if available. Else, it raises an error and the process will be stopped.

## Error: ... do_fetch: Fetcher failure: ...
  This error indicates that there downloading some files has been faced serious problems because of internet connection problems. Try to check your internet connection or change it if possible. Then, execute Bitbake command again. Note that executing the Bitbake command after facing an error, does not start the process from the beginning and continues from the last successfull task done.

## Error: The postinstall intercept hook '...' failed
  This error relates to executing some commands in the files located in this path:
<pre>
  <code>
    openbmc/poky/scripts/postinst-intercepts
  </code>
</pre>
To solve this error, you should change the permission of these files in the path to 644. Then, run the Bitbake command again.
<pre>
  <code>
update_gio_module_cache     
update_mime_database
postinst_intercept       
update_gtk_icon_cache       
update_pixbuf_cache
update_desktop_database  
update_gtk_immodules_cache  
update_udev_hwdb
update_font_cache        
update_mandb
  </code>
</pre>
  

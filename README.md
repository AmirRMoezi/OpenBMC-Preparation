# How to prepare OpenBMC
In this instructional guide, you will acquire the knowledge necessary to prepare OpenBMC as an open source embedded Linux for implementation on the AST2500-EVB Board.
<p align="center">
  <img width="300" height="300" src="https://github.com/AmirRMoezi/OpenBMC/blob/main/OpenBMC_logo.png">
</p>

## Step 1
Opt for Ubuntu 22.04 LTS as your preferred host operating system. Proceed with its installation on your computer or within a virtual environment, such as VMWare. Please ensure the inclusion of updates during the Ubuntu installation process. Upon the successful completion of the installation procedure, kindly perform a thorough check for updates utilizing the Software Updater application. Subsequently, refresh the package index files within the system by executing the 'sudo apt-get update' command in the terminal.

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
It is essential to set up your build environment according to your target hardware. run the following command to get supported taget hardwares list:
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

Subsequently, a directory named evb-ast2500 is created within the openbmc/build directory. Should you come across any warning messages pertaining to insufficient permissions to chmod a file, please initiate a new terminal session and modify the permissions of all files and directories within openbmc/build/evb-ast2500 to 777 using the 'sudo' command. Following this, proceed to execute the `. setup` command once more in the initial terminal session, this time omitting the 'sudo' prefix. Ultimately, the output of this command will manifest as follows:

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
Within the same terminal where you executed the '.setup' command, proceed to input the following command:
<pre>
<code>  
$ bitbake obmc-phosphor-image
</code>  
</pre>
Bitbake is a build tool for embedded Linux cross compilation. It compiles your OpenBMC.

## Step 6
### Everything is ready. Run your OpenBMC
Upon the successful compilation of OpenBMC, the requisite images essential for the execution of OpenBMC on both QEMU and an actual board have been prepared. These images are situated within the following directory:
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
Please remember that your '.mtd' file might have a different name, like 'obmc-phosphor-image-evb-ast2500-20230813043725.static.mtd'. So, you need to adjust the filename in the command accordingly. This image is in the repository.
To run OpenBMC on an actual AST2500 evaluation board, you should first extract a compressed file with the '.mtd.all.tar' extension. Inside this file, you'll find a 'image-bmc' file. Just add '.bin' to its name and use that file to program the board. This image is also available in the repository.

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
The instructions highlight that you should avoid utilizing root access within the terminal where you're executing the 'setup' and 'bitbake' commands, as outlined in Step 4.
## Fetcher failure for URL: 'https://yoctoproject.org/connectivity.html'
This error message indicates an issue with your internet connection, or it's possible that your ISP or network is obstructing the provided URL. Please verify your connection and if the problem persists, consider adding 'CONNECTIVITY_CHECK_URIS = "URL"' to this file:
<pre>
<code>
openbmc/poky/meta/conf/sanity.conf
</code>
</pre>
Substitute 'URL' with any accessible URL from your network. This is the method that Bitbake employs to verify internet connectivity.

## Warning: ... do_fetch: Failed to fetch URL ..., attemting mirrors if available
This warning means that some files couldn't be downloaded from the URL mentioned due to internet connection problems. The system tries other links if there are any available. If not, an error occurs and the process stops.

## Error: ... do_fetch: Fetcher failure: ...
This error means that downloading certain files has encountered significant problems due to internet connection issues. Please check your internet connection or consider switching it if feasible. Once that's done, try running the Bitbake command again. It's important to note that if you execute the Bitbake command after encountering an error, it won't restart the process from scratch; it'll resume from the point of the last successfully completed task.

## Error: The postinstall intercept hook '...' failed
This error is associated with running specific commands found within the files located at this particular path:
<pre>
<code>
openbmc/poky/scripts/postinst-intercepts
</code>
</pre>
To address this error, you need to modify the permissions of the files listed below within the specified path to 644. Afterward, proceed to execute the Bitbake command again.
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

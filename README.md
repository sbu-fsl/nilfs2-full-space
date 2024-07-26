# NILFS2 Full Space Bug Reproduction

We have prepared a scripts to reproduce a Full Space Bug in NILFS2. We have used Linux Kernel v6.9.2 and v6.9.4 in our experiments, however, we recommend using v6.9.4. A sample log, captured while this problem was triggered using our script on NILFS2 (mounted on a ramdisk with Linux v6.9.4), is available in this repository as `example_output.sh`.

### Setting up the Environment
We need to install Linux Kernel v6.9.4 before using the replayer. We have provided a kernel config, `.kernel-6.9.4-config`, that corresponds to the kernel used by us during the above experiment. On a Linux machine, follow the below steps to setup a Kernel with v6.9.4.

(**Note: Reminder to backup/snapshot your machine before installing a new Kernel.**)

* First clone the respository using the following git command:
> git clone https://github.com/sbu-fsl/nilfs2-full-space.git

This will clone the repo in a folder name `nilfs2-full-space`

* To compile the kernel, you will need to install some packages, for Debian/Ubuntu Linux (the distro used for our experiments) use the below commands for installation:
> sudo apt-get update
> sudo apt-get install build-essential libncurses-dev bison flex libssl-dev libelf-dev make

* Then clone the Kernel source code, extract it, and then `cd` into the folder:
> wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.9.4.tar.xz

> tar -xf linux-6.9.4.tar.xz

> cd linux-6.9.4

* Copy the provided kernel config to the Linux kernel source directory and name it `.config` (the command assumes that `nilfs2-full-space` has been cloned in the root folder):
> cp ~/nilfs2-full-space/.kernel-6.9.4-config .config

* Use the configuration tool to ensure your `.config` file is correctly set up.
> make oldconfig

* Compile the kernel
> make -j$(nproc)

* Compile kernel modules
> sudo make modules_install

* Install the compiled kernel
> sudo make install

* After executing the above steps, reboot your machine 
> sudo reboot

* Then verify that you have successfully installed Linux Kernel v6.9.4
> uname -mars

### NILFS2 Settings
* To work with NILFS2, you need to install the NILFS tools
> sudo apt-get install nilfs-tools

* Open the nano editor to modify the `nilfs_cleanerd.conf` file 
> sudo nano /etc/nilfs_cleanerd.conf

In the editor, change the value of the `protection_period` parameter to 10 (from the default 3600) to make the cleaner run more frequently

Save and exit the editor

### Running the script

* Navigate to the directory where the script is located
> cd ~/nilfs2-full-space/

* Make the script executable
> chmod +x stuck_at_100.sh

* Run the script
> sudo ./stuck_at_100.sh > ~/nilfs2-full-space/output.log 2>&1


===============================
13.0	Steps to Apply RT Patch
===============================

-----------------------------
13.1 	Install prerequisites
-----------------------------

Install all the prerequisites using the following command

*sudo apt-get install -y libncurses-dev libssl-dev bison flex*

*build-essential wget*

.. note::
   
   It will prompt to update package runtime, click Yes to proceed with the update.

--------------------------------------------
13.2 	Keyboard shortcuts for menuconfig UI
--------------------------------------------

.. list-table:: 
   :widths: 25 25 25
   :header-rows: 1

   * - #
     - Task/Use
     - Keyboard Key
   * - 1
     - To select specific kernel feature
     - Space bar 
   * - 2
     - To come out of current window
     - Esc Esc 
   * - 3
     - To save current setting 
     - Click on <save> button
   * - 4
     - Exit     
     - Click on <exit> button 

---------------------------------------
13.3 	Steps to apply PREEMPT_RT patch
---------------------------------------

This section provides steps to apply PREEMPT_RT patch

**Steps:**

1.	Make a working directory on system

*$ mkdir ~/kernel && cd ~/kernel*

2.	Download kernel in ~kernel directory created in step 1

•	Download kernel manually 

**Link for download** - https://www.kernel.org/pub/linux/kernel/

This will download kernel manually or use following command to download it from command line inside current directory.

*$ wget https://mirrors.edge.kernel.org/pub/linux/kernel/v4.x/linux-4.19.72.tar.gz*

**Recommendation:** Please get Linux kernel version 4.19.72

•	Download preempt RT patch

**Link for download -** https://www.kernel.org/pub/linux/kernel/projects/rt/ this will download patch manually or use following command to download it from command line inside current directory.

*wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/4.19/older/patch-4.19.72-rt26.patch.gz*

**Recommendation:** Please get PREEMPT_RT version 4.19.72-rt26 

3.	Unzip the kernel using following command

*$ tar -xzvf linux-4.19.72.tar.gz*

4.	Patch the kernel

*$ cd linux-4.19.72*

.. code-block:: sh

  *$ gzip -cd ../patch-4.19.72-rt26.patch.gz | patch -p1 --verbose*

.. note::

    The UWC Software has been tested with ubuntu OS 18.04.3 LTS version, the aforementioned ubuntu kernel version (4.19.72) & the corresponding RT kernel patch (patch-4.19.72-rt26). 
    If users have a different version of ubuntu 18.04.X LTS version (where "X' can change based on the version of 18.04 being downloaded), then kindly check the corresponding kernel
    version of the ubuntu and map it with the right RT kernel patch. Use the same steps as above to download & patch kernel & RT patches.
    Can refer to the links "https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/", "https://mirrors.edge.kernel.org/pub/linux/kernel/" for mapping kernel version with RT kernel patch


5.	Launch the graphical UI for setting configurations

The next command launches a graphical menu in the terminal to generate the .config file.

*$ make menuconfig*

**Graphical UI is shown below:**

.. figure:: Doc_Images/image9.png
    :scale: 60 %
    :align: center

    Figure 11.1: Main launching screen

6.	Select the preemption model as Basic RT using tab key on keyboard

    1)	Select and enter on “General setup” option.
    2)	Select and Enter on Preemption Model (Voluntary Kernel Preemption (Desktop))
    3)	Select and Enter on Preemption Model (Fully Preemptible Kernel (RT))
    4)	After successful selection click on save button and then come back to main page using Esc button on keyboard. 

Refer the following screen capture for more details

.. figure:: Doc_Images/image10.png
    :scale: 60 %
    :align: center

    Figure 11.2: Preemption Model (Voluntary Kernel Preemption (Desktop))

.. figure:: Doc_Images/image11.png
    :scale: 60 %
    :align: center

    Figure 11.3: Preemption Model (Fully Preemptible Kernel (RT))

.. figure:: Doc_Images/image12.png
    :scale: 60 %
    :align: center

    Figure 11.4 Fully Preemption Kernel (RT)

Save and exit

To save the current setting click on *<save>* button and then exit the UI using *<exit>* button.

.. figure:: Doc_Images/image13.png
    :scale: 60 %
    :align: center

    Figure 11.5 Click on ‘OK’

7.	Compile the kernel (Execute the following commands)

*$ make –j20*

*$ sudo make INSTALL_MOD_STRIP=1 modules_install -j20*

*$ sudo make install -j20*


8.	Verify and update Verify that initrd.img-4.19.72-rt26, vmlinuz-4.19.72-rt26, and config-4.19.72-rt26 are generated in /boot directory and update the grub.

*$ cd /boot*

*$ ls*

*$ sudo update-grub*

Verify that there is a menuentry containing the text "menuentry 'Ubuntu, with Linux 4.9.72-rt26'" in */boot/grub/grub.cfg* file

To change default kernel in grub, edit the *GRUB_DEFAULT* value in */etc/default/grub* to your desired kernel.

.. note::
   
   0 is the 1st menuentry

9.	Reboot and verify using command

*$ sudo reboot*

Once the system reboots, open the terminal and use uname -a to check the kernel version

Command will show below output for successfully applied RT patch – 
*Linux ubuntu 4.19.72-rt26 #1 SMP PREEMPT RT Tue Mar 24 17:15:47 IST 2020 x86_64 x86_64 x86_64 GNU/Linux*

------------------
13.4	References
------------------

https://stackoverflow.com/questions/56189710/how-to-enable-config-rt-group-sched-in-ubuntu-to-make-it-rt

https://stackoverflow.com/questions/51669724/install-rt-linux-patch-for-ubuntu

https://unix.stackexchange.com/questions/270390/how-to-reduce-the-size-of-the-initrd-when-compiling-your-kernel/270418


# Compiling on the Acer C720P in ChromeOS

Oftentimes when using my C720/C720P Chromebook I find myself wanting
tools that I often use in regular Linux desktops. So far, I had
just been resigning myself to the fact that I needed to, at the
very least, switch to a Linux chroot in order to use these tools.
Today, I decided to dig a little into what it would take to build
these programs so that I could run them natively in Chrome OS.

## Developer mode

This guide assumes you are in developer mode. If you are not in
developer mode or do not know what this is, you will not be able
to complete this guide. To enable developer mode, find your
model [here](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices)
and follow the guide.

You may also need to disable rootfs verification in order to
modify the root filesystem.

    sudo /usr/share/vboot/bin/make_dev_ssd.sh --remove_rootfs_verification --partitions 2

## Install the build toolchain

Chrome OS on the C720/C720P uses a 64 bit CPU using the AMD64 instruction
set. Find the latest toolchain at
http://distfiles.gentoo.org/releases/amd64/autobuilds/current-stage3-amd64/.
Open a developer shell and run the following commands replacing the filenames
with the correct versions.

Note: the tar command will throw errors if you do not run it as root
because it cannot set up the special files in the dev folder. This is
not an issue for us and can be safely ignored.

    cd ~
    mkdir toolchain
    cd toolchain
    wget http://distfiles.gentoo.org/releases/amd64/autobuilds/current-stage3-amd64/stage3-amd64-20140814.tar.bz2
    tar xf stage3-amd64-20140814.tar.bz2

The next step is to remount the home partition with options to allow
us to execute binaries.

    sudo mount -i -o remount,exec /home/chronos/user/

Next, we need to set up some environment variables used by the toolchain.
Remember to change the file names to reflect the current version of the toolchain.

    export C_INCLUDE_PATH=~/toolchain/usr/include
    export LD_LIBRARY_PATH=/home/chronos/user/toolchain/usr/lib:/home/chronos/user/toolchain/usr/lib/binutils/x86_64-pc-linux-gnu/2.23.2
    export PATH=$PATH:~/toolchain/usr/x86_64-pc-linux-gnu/gcc-bin/4.7.3:~/toolchain/usr/x86_64-pc-linux-gnu/binutils-bin/2.23.2

And patch the `libc.so` in to toolchain to use our path

    sed -i 's/\/usr/\/home\/chronos\/user\/toolchain\/usr/g' ~/toolchain/usr/lib/libc.so

## Build Make

The first thing I built with the toolchain was GNU Make.
This will make it easier to build other tools because
make makes everything better :).

    cd ~
    wget http://ftp.gnu.org/gnu/make/make-3.82.tar.bz2
    tar xf make-3.82.tar.bz2
    cd make-3.82
    ./configure
    ./build.sh
    sudo cp make /usr/local/bin

## Compiling Programs

### htop

    cd ~
    wget http://hisham.hm/htop/releases/1.0.3/htop-1.0.3.tar.gz
    tar xf htop-1.0.3
    ./configure
    make
    sudo cp htop /usr/local/bin

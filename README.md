# Dimebag-dev
<strong>$10 Riscv Linux with DigitalOcean Droplet</strong>



  <strong>First:</strong><br> 
  Go to <strong>DigitalOcean</strong> (https://www.digitalocean.com/) and get a $5.00 Droplet (vm instance) and $5.00 of block storage<br><br>
  Something like this -> / 1 GB Memory / 1 Intel vCPU / 25 GB Disk + 50 GB / NYC1 - Ubuntu 22.04 x64<br>
  SSH into your fresh new Ubuntu instance.<br><br>

  <strong>Prereqs:</strong><br>
  apt-get install apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo   gperf libtool patchutils bc zlib1g-dev libexpat-dev git<br>




  <strong>Second:</strong><br>
  Go get the following:<br>
  Toolchain:<br>
  git clone https://github.com/riscv/riscv-gnu-toolchain <br><br>

  
   <strong>Third:</strong><br>
   Build the Toolchain (crosscompiler)<br>
  ./configure --prefix=/opt/riscv <strong><-whatever path you choose this will probably work ootb</strong><br>
   make linux<br><br>
  
   <strong>Fourth:</strong><br>
   Make a directory that looks something like this and move qemu,linux,and busy box there:<br>
   mkdir riscv64-linux<br>
   and<br>
   cd riscv64-linux<br><br>
   Then get<br>
   System Emulator:<br>
   git clone https://github.com/qemu/qemu <br>
   Linux kernel:<br>
   git clone https://github.com/torvalds/linux <br>
   Linux userland:<br>git clone https://git.busybox.net/busybox <br><br>
  
   
    
   <strong>Fifth:</strong><br>
   <strong>Note this step uses the local compiler NOT the compiler from tools above</strong><br>
   cd qemu<br>
   git checkout v5.0.0<br>
   ./configure --target-list=riscv64-softmmu<br>
   make -j $(nproc)<br>
   make install<br><br>

   <strong>Sixth:</strong><br>
   cd linux<br>
   <strong>Note this step uses the compiler from tools above and it should be in your path e.g. export PATH=/opt/riscv/bin:$PATH </strong><br>
   git checkout v5.4.0<br>
   make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- defconfig<br>
   make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- -j $(nproc)<br><br>
   
  <strong>Seventh:</strong><br>
  cd busybox<br>
  git checkout 1_32_0<br>
  make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- defconfig<br>
  make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- menuconfig<br>
  <strong>!!!Check Settings in the menu that pops up - Build busybox with static binary (no shared libs)!!</strong><br>
  make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- install<br>
  cd ..<br><br>
   
  <strong>Eighth:</strong><br>
  <strong>Trickiest Step</strong><br>
  Create root filesystem image<br>
  mkdir rootfs<br>
  cd rootfs<br>
  dd if=/dev/zero of=rootfs.img bs=1M count=50<br>
  mkfs.ext2 -L riscv-rootfs rootfs.img<br>
  mkdir /mnt/rootfs<br>
  mount rootfs.img /mnt/rootfs<br>
  cp -ar ../busybox/_install/* /mnt/rootfs<br>
  mkdir /mnt/rootfs/{dev,home,mnt,proc,sys,tmp,var}<br>
  chown -R -h root:root /mnt/rootfs<br>
  umount /mnt/rootfs<br>
  cd ..<br>
  
  <strong>Ninth:</strong><br>
  Run it!!!<br>
  qemu-system-riscv64 -nographic -machine virt -kernel linux/arch/riscv/boot/Image -append "root=/dev/vda rw console=ttyS0" -drive  file=rootfs/rootfs.img,format=raw,id=hd0 -device virtio-blk-device,drive=hd0 -bios default <br>

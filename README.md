# Dimebag-dev
<strong>Riscv Linux with DigitalOcean Droplet & Storage For Less Than $10.00</strong>



  <strong>First:</strong><br> 
  Go to <strong>DigitalOcean</strong> (https://www.digitalocean.com/) and get a $4.00 Droplet and $5.00 of block storage (Easy Instructions)<br><br>
  Something like this -> / 1 GB Memory / 1 Intel vCPU / 25 GB Disk + 50 GB / NYC1 - Ubuntu 22.04 x64<br>
  <strong>SSH into your fresh new Ubuntu instance. </strong><br><br>

  <strong>Prereqs:</strong><br>
apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev git<br>




  <strong>Second:</strong><br>
  
  Make a directory that looks something like this:<br>
  mkdir riscv64-linux<br>
  and<br>
  cd riscv64-linux<br><br>
  Get the Riscv Toolchain:<br>
  git clone https://github.com/riscv/riscv-gnu-toolchain <br><br>

  Build the Toolchain (crosscompiler)<br>
  ./configure --prefix=/opt/riscv <strong><-whatever path you choose this will probably work ootb</strong><br>
   make linux<br><br>
  
  <strong>Third:</strong><br>
   
   Then<br>
   cd ..<br>
   mkdir qemu<br>
   cd qemu<br>
   Get the System Emulator:<br>
   git clone https://github.com/qemu/qemu <br>

  <strong>Uses the local compiler to build it NOT the compiler in the ricsv toolchain</strong><br>
   cd qemu<br>
   git checkout v5.0.0<br>
   ./configure --target-list=riscv64-softmmu<br>
   make -j $(nproc)<br>
   make install<br><br>
   cd..<br>
  
  <strong>Fourth:</strong><br>
 
  
  mkdir linux<br>
  cd linux<br>
   Get the Linux kernel:<br> 
   git clone https://github.com/torvalds/linux <br>
   <strong>Note this step uses the compiler from tools above and it should be in your path e.g. export PATH=/opt/riscv/bin:$PATH </strong><br>
   git checkout v5.4.0<br>
   make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- defconfig<br>
   make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- -j $(nproc)<br><br>
   cd..<br>
  
  <strong>Fifth:</strong><br>
  mkdir busybox<br>
  cd busybox<br>
  Get Linux userland:<br>git clone https://git.busybox.net/busybox <br><br>
  git checkout 1_32_0<br>
  make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- defconfig<br>
  make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- menuconfig<br>
  <strong>!!!Check Settings in the menu that pops up - Build busybox with static binary (no shared libs)!!</strong><br>
  make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- install<br>
  cd ..<br><br>
   
  <strong>Sixth:</strong><br>
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
  
  <strong>Last:</strong><br>
  Run it!!!<br>
  qemu-system-riscv64 -nographic -machine virt -kernel linux/arch/riscv/boot/Image -append "root=/dev/vda rw console=ttyS0" -drive  file=rootfs/rootfs.img,format=raw,id=hd0 -device virtio-blk-device,drive=hd0 -bios default <br><br>
  
  Add HW extensions (Verilog -> c/c++)? https://www.veripool.org/verilator/ <br>
  Friends in the embedded free world: https://www.embecosm.com/ <br>

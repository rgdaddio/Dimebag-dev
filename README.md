# Dimebag-dev
$10 Riscv Linux with DigitalOcean Droplet



<strong>First:</strong><br> 
Go to <strong>DigitalOcean</strong> (https://www.digitalocean.com/) and get a $5.00 Droplet (vm instance) and $5.00 of block storage<br><br>
Something like this -> / 1 GB Memory / 1 Intel vCPU / 25 GB Disk + 50 GB / NYC1 - Ubuntu 22.04 x64<br>
Log into your fresh new Ubuntu instance.<br><br>

<strong>Second:</strong><br>
Go get the following:<br>
Toolchain:<br>
git clone https://github.com/riscv/riscv-gnu-toolchain <br>
System Emulator:<br>
git clone https://github.com/qemu/qemu <br>
Linux kernel:<br>
git clone https://github.com/torvalds/linux <br>
Linux userland:<br>
git clone https://git.busybox.net/busybox <br><br>
  
  <strong>Third:</strong><br>
  Build the chain

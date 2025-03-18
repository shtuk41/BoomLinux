# BoomLinux

This is a tiny distro of Linux OS called Boom. 

References:

Nir Luchtman. https://www.youtube.com/watch?v=u2Juz5sQyYQ  
X86 calling conventions, https://en.wikipedia.org/wiki/X86_calling_conventions  
Intel, Instruction Set Reference, SYSCALL - Fast System Call  
System calls header - arch/x86/include/generated/asm/syscalls_64.h  

Build instructions:

Clone Linux kernel to a linux directory:

In Linux directory:

$make tinyconfig
$make menuconfig

enable the following:  
&nbsp;	64-bit kernel  
&nbsp;	General Setup  
&nbsp;&nbsp;&nbsp;	Initial RAM filesystem and RAM disk (initframfs/initrd) support  
&nbsp;&nbsp;&nbsp;	Configure standard kernel features (expert users)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;	Enable support for printk  
&nbsp;&nbsp;&nbsp;	Device drivers  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;		Character Devices  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;	Enable TTY  
&nbsp;	Executable file formats  
&nbsp;&nbsp;&nbsp;	Kernel support for ELF binaries  
		
$make -j 4  

Previous command creates arch/x86/boot/bzImage

Test it with qemu:

$qemu-system-x86_64 -kernel arch/x86/boot/bzImage

Next build a user space.  This is done in shell.c.  The idea is that instead of using system commands linkedin to libc, create sys.S file with system calls implemented in assembly.

$gcc -c -Os -fno-stack-protector -o shell.o shell.c  
$as sys.S  
$ld -o shell shell.o a.out --entry main -z noexecstack  
$mv shell init  

Download lua, configure Makefile to build static version, and build

MYLDFLAGS=-static

Move lua to files

$echo lua >> files  
$echo init >> files

Create an archive:

$cat files | cpio -H newc -o > init.cpio

Create isoimage from the linux directory:

$make isoimage FDARGS="initrd=/init.cpio" FDINITRD=~/workingdir/init.cpio

$qemu-system-x86_64 -cdrom arch/x86/boot/image.iso

Try running from VirtualBox.









			
		





